# Proximity Service Design

Building a system to find nearby places like Yelp or Google Maps.

---

## Requirements

### Functional Requirements

- Find businesses/places near a location
- Filter by category (restaurants, gas stations, etc.)
- Return results ranked by distance, rating, or relevance
- Show business details (hours, reviews, photos)
- Support business registration and updates

### Non-Functional Requirements

- Low latency (< 200ms for search)
- High read throughput (read-heavy workload)
- Reasonable write latency (business updates not real-time critical)
- Scalable to millions of businesses

---

## Scale Estimation

**Assumptions (Yelp scale):**
- 200 million businesses worldwide
- 500 million daily searches
- Read:write ratio = 1000:1

**Calculations:**

Searches per second: 500M / 86400 ≈ 5,800 QPS

Peak: 3x ≈ 17,400 QPS

Storage: 200M × 10 KB per business = 2 TB

This is read-heavy with geospatial queries.

---

## High-Level Architecture

```
┌─────────────────────────────────────────────────────────────────┐
│                    Proximity Service                            │
└─────────────────────────────────────────────────────────────────┘

┌────────────────┐
│   Mobile App   │
│   / Website    │
└───────┬────────┘
        │
        ▼
┌────────────────┐
│   API Gateway  │ ←─ Rate limiting, auth, caching
└───────┬────────┘
        │
   ┌────┴────┐
   ▼         ▼
┌──────┐  ┌──────────────┐
│Search│  │   Business   │
│  API │  │     API      │
└──┬───┘  └──────┬───────┘
   │             │
   ▼             ▼
┌──────┐  ┌──────────────┐
│ Geo  │  │   Business   │
│Index │  │    Store     │
└──────┘  └──────────────┘
```

---

## Geospatial Indexing

Finding nearby points efficiently requires specialized data structures.

### The Problem

Naive approach: Calculate distance to every business, filter by radius.

```
SELECT * FROM businesses
WHERE distance(lat, lng, user_lat, user_lng) < 5km
```

**Problem:** O(N) for every query. Doesn't scale.

### Solution: Spatial Indexes

Index data by location to prune search space.

---

## Geohash

Convert 2D coordinates into 1D string.

### How It Works

Divide world into grid. Each cell gets a code.

```
Level 1: World divided into 32 cells (0-9, a-z)
Level 2: Each cell divided into 32 subcells
...

Example:
  (37.7749, -122.4194) → "9q8yy"
```

### Properties

- **Prefix property:** Nearby locations share prefixes
- "9q8yy" and "9q8yz" are neighbors
- Longer string = smaller area = more precision

### Precision Levels

| Geohash Length | Cell Size |
|----------------|-----------|
| 4 | ~39 km × 19 km |
| 5 | ~5 km × 5 km |
| 6 | ~1.2 km × 600 m |
| 7 | ~150 m × 150 m |
| 8 | ~38 m × 19 m |

### Querying

Find businesses in 5km radius:

1. Calculate geohash for user location (precision 5)
2. Get neighboring geohashes (8 neighbors + center = 9 cells)
3. Query: `WHERE geohash IN (neighbors)`
4. Filter results by exact distance

### Edge Cases

**Boundary problem:** Two businesses 10 meters apart might have different geohash prefixes if they're on cell boundary.

**Solution:** Always query neighboring cells, not just the user's cell.

---

## Quadtree

Tree structure for 2D space.

### How It Works

1. Start with region covering entire area
2. If region has > threshold points, divide into 4 quadrants
3. Recursively divide until each leaf has ≤ threshold
4. Dense areas have deeper trees

```
         [World]
        /   |   \   \
      NW   NE   SW   SE
     /|\  /|\  ...
    ...  ...
```

### Querying

1. Find leaf node containing user location
2. Also check neighboring nodes within radius
3. Return businesses from relevant nodes

### Pros
- Adapts to density (sparse areas = fewer nodes)
- Efficient updates

### Cons
- More complex than geohash
- Tree rebalancing on updates

---

## R-Tree

Balanced tree for spatial data.

### How It Works

- Each node contains bounding rectangles
- Leaf nodes contain actual points
- Internal nodes contain minimum bounding rectangles (MBRs)

### Querying

1. Start at root
2. Only descend into nodes whose MBR intersects query region
3. At leaves, check individual points

### Used By

PostGIS, SQLite R*Tree extension.

---

## Choosing an Approach

| Approach | Best For | Notes |
|----------|----------|-------|
| Geohash | Simple implementation, key-value stores | Easy to shard |
| Quadtree | In-memory index, varying density | Good for custom needs |
| R-Tree | Relational databases, complex queries | Built into PostGIS |

For most cases: **Geohash** for simplicity, **PostGIS R-Tree** if using PostgreSQL.

---

## Database Design

### Business Table

```
businesses:
  id
  name
  description
  category
  latitude
  longitude
  geohash (computed)
  address
  phone
  hours
  rating
  review_count
  created_at
  updated_at
```

### Indexes

```
- Primary: id
- Geohash prefix: geohash (for proximity queries)
- Category: category (for filtering)
- Composite: (geohash, category) for combined queries
```

---

## Search Flow

### 1. Parse Request

```
Input: latitude, longitude, radius (optional), category (optional)
```

### 2. Calculate Search Area

```
geohash = encode(latitude, longitude, precision=5)
neighbors = get_neighbors(geohash)  // 8 surrounding cells
search_cells = [geohash] + neighbors
```

### 3. Query Database

```
SELECT * FROM businesses
WHERE geohash IN (search_cells)
  AND category = ?  // if filtered
ORDER BY distance(lat, lng, ?, ?) ASC
LIMIT 20
```

### 4. Post-Process

- Calculate exact distances
- Filter out results beyond radius
- Rank by distance/rating/relevance

---

## Caching

Proximity queries are highly cacheable.

### What to Cache

- Popular areas (downtown, airports, tourist areas)
- Recent searches (same location, same query)
- Business details (don't re-fetch on every view)

### Cache Key Design

```
Key: nearby:{geohash}:{category}:{radius}
TTL: 5 minutes (businesses don't change often)
```

### Invalidation

On business update:
1. Get business's geohash
2. Invalidate cache keys containing that geohash

---

## Ranking

Not all nearby results are equal.

### Factors

1. **Distance:** Closer is better (usually)
2. **Rating:** Higher rated preferred
3. **Review count:** More reviews = more trusted
4. **Relevance:** Matches query terms
5. **Business status:** Open now > closed
6. **Sponsorship:** Paid placement (ads)

### Scoring

```
score = w1 * (1/distance) + w2 * rating + w3 * log(reviews)
```

Weights tuned based on product goals.

---

## Real-Time Business Updates

### Business Registration

New business → insert into database → update geo index.

### Business Update

Changed hours/location → update database → invalidate cache.

### Index Update Strategy

**Immediate:** Update geo index on every change.
- Simple but more load

**Batched:** Queue updates, apply periodically.
- More efficient but slight delay

For most proximity services, batched (every few minutes) is acceptable.

---

## Sharding

As data grows, need to distribute.

### By Geohash

```
Shard 0: geohash.startsWith('0-7')
Shard 1: geohash.startsWith('8-f')
Shard 2: geohash.startsWith('g-n')
Shard 3: geohash.startsWith('o-z')
```

### Challenge

Hot spots: Times Square has more businesses than rural areas.

**Solution:** Dynamic sharding or over-partition + rebalance.

---

## Common Mistakes

**No spatial index.** Full table scan for every query.

**Querying only the user's cell.** Misses businesses on boundaries.

**Distance sort in application.** Let database do it with proper index.

**No caching.** Same popular areas queried repeatedly.

**Cache key too specific.** lat/lng with too many decimal places = no cache hits.

---

## What An Experienced Senior Engineer Thinks About

**Hot spot handling.** Popular areas need more capacity.

**Freshness requirements.** How quickly must new business appear in search?

**International considerations.** Different regions have different data densities.

**Search quality metrics.** How do you know results are good?

---

## Vibe Engineering Guide

When prompting about proximity services:

**Less useful:**
> "Build a nearby places feature"

**More useful:**
> "Design a service to find nearby restaurants:
> - 10 million businesses in database
> - 10,000 search queries per second
> - Return top 20 results within radius
> - Support category filtering
>
> Focus on: geospatial indexing approach, how to handle queries near cell boundaries, and caching strategy for popular areas."

**For specific problems:**
> "We're using geohash but missing results near the edge of cells. User at cell boundary doesn't see business 50 meters away in adjacent cell. How do we fix this?"

---

## Quick Check

<details>
<summary><b>Why not calculate distance to every business?</b></summary>

O(N) per query doesn't scale. With 200M businesses, you can't scan all of them. Spatial indexes prune search space to O(log N) or better.

</details>

<details>
<summary><b>What's the geohash boundary problem?</b></summary>

Two nearby locations might have very different geohashes if they're on a cell boundary. Solution: always query neighboring cells too, not just the user's cell.

</details>

<details>
<summary><b>How do you handle areas with high business density?</b></summary>

Hot spots (city centers) need more capacity. Options: finer-grained sharding, caching popular areas aggressively, read replicas for hot regions.

</details>

<details>
<summary><b>What precision geohash to use?</b></summary>

Depends on typical search radius. For 5km search: geohash length 5 (~5km cells). Query center cell + 8 neighbors to cover search area.

</details>

---

Next: [Level 8: Senior Engineer Thinking](../08-senior-engineer-thinking/README.md)
