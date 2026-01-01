# Search Systems

How search works beyond simple database queries.

---

## Why Not Just Use SQL?

SQL LIKE queries have limitations:

```sql
SELECT * FROM products WHERE description LIKE '%wireless headphones%'
```

**Problems:**
- Slow (table scan, indexes don't help with leading wildcards)
- No relevance ranking (which result is best?)
- No tolerance for typos ("headpones" finds nothing)
- No understanding of synonyms ("earbuds" vs "headphones")
- Poor performance at scale

Search engines solve these problems.

---

## How Search Engines Work

### Inverted Index

The core data structure. Instead of document → words, it maps word → documents.

```
Forward index (what you have):
Doc 1: "wireless bluetooth headphones"
Doc 2: "wired studio headphones"
Doc 3: "wireless mouse"

Inverted index (what search engines build):
wireless → [Doc 1, Doc 3]
bluetooth → [Doc 1]
headphones → [Doc 1, Doc 2]
wired → [Doc 2]
studio → [Doc 2]
mouse → [Doc 3]
```

**Query "wireless headphones":**
- "wireless" → [Doc 1, Doc 3]
- "headphones" → [Doc 1, Doc 2]
- Intersection → [Doc 1]

---

## Text Analysis

Before building the index, text is processed:

**Tokenization:** Split text into words.

**Lowercasing:** "Headphones" → "headphones"

**Stemming:** "running" → "run", "headphones" → "headphone"

**Stop words:** Remove common words like "the", "a", "is"

**Synonyms:** Expand "tv" to include "television"

This means searching for "Running" finds documents containing "run", "runs", "running".

---

## Relevance Ranking

Multiple documents might match. How do you order them?

### TF-IDF (Term Frequency - Inverse Document Frequency)

A word is more important if:
- It appears frequently in this document (TF)
- It's rare across all documents (IDF)

"Headphones" appearing 5 times in a document about headphones is more significant than "the" appearing 50 times.

### BM25

An improvement on TF-IDF. Considers document length. Used by Elasticsearch and others.

### Other Signals

Modern search adds:
- Recency (newer might be better)
- Popularity (more views/clicks)
- Personalization (user's history)
- Field boosts (title matches > description matches)

---

## Common Search Engines

**Elasticsearch:** Most popular. REST API, distributed, powerful.

**OpenSearch:** Fork of Elasticsearch, AWS-backed. Basically the same.

**Solr:** Apache project. Similar to Elasticsearch, older.

**Meilisearch:** Simpler, faster for smaller datasets, good developer experience.

**Algolia:** Managed service. Easy to use, expensive at scale.

For most use cases, Elasticsearch/OpenSearch is the standard choice.

---

## Search Architecture

Search engines are typically separate from your primary database.

```
User writes → Primary DB (PostgreSQL)
                    ↓
              Change events
                    ↓
              Index to Search (Elasticsearch)
                    ↓
User searches → Search Engine → Return results
                    ↓
              Fetch full data from Primary DB
```

**Primary DB:** Source of truth.
**Search engine:** Optimized for search, not writes.

Keep them in sync via:
- Application-level updates (write to both)
- Change data capture (database triggers events)
- Periodic re-indexing

---

## What to Index

Not everything in your database belongs in the search index.

**Index:** Fields users will search (title, description, tags)
**Maybe index:** Metadata for filtering (category, price, date)
**Don't index:** Internal IDs, sensitive data, rarely-searched fields

More fields = larger index = slower performance.

---

## Common Features

**Autocomplete / Typeahead:** Suggest completions as user types. Uses edge n-grams or completion suggesters.

**Faceted search:** Filter by categories, price ranges, etc. "Show only Electronics under $50."

**Fuzzy matching:** Find "headpones" when user meant "headphones". Edit distance algorithms.

**Highlighting:** Show the matching parts of results in bold.

**"Did you mean?":** Suggest corrections for misspelled queries.

---

## Trade-offs

**Pros of dedicated search:**
- Much faster for text search
- Relevance ranking
- Typo tolerance and fuzzy matching
- Scales well for search workloads

**Cons:**
- Another system to manage
- Data synchronization complexity
- Eventual consistency (slight delay between DB update and searchable)
- Not a replacement for your primary database

---

## Common Mistakes

**Using search as primary database.** It's not designed for that. Use it alongside your database.

**Indexing everything.** Leads to huge indexes and slow performance.

**Ignoring relevance tuning.** Default ranking is rarely optimal. Test and tune.

**Not handling sync failures.** What happens if indexing fails? You need monitoring.

**Over-engineering for small data.** If you have 10,000 products, PostgreSQL full-text search might be enough.

---

## PostgreSQL Full-Text Search

For simpler needs, PostgreSQL has built-in search:

```sql
SELECT * FROM products 
WHERE to_tsvector('english', description) @@ to_tsquery('wireless & headphones');
```

Works well for:
- Smaller datasets
- Simpler requirements
- When you don't want another system

Doesn't scale to millions of documents or complex relevance needs as well as Elasticsearch.

---

## Vibe Engineering Guide

When prompting about search systems:

**Less useful:**
> "Add search to my app"

**More useful:**
> "I'm building product search for an e-commerce site with 500k products. Users need to:
> - Search by product name and description
> - Filter by category, price range, and rating
> - Get typo tolerance ('headpones' should find 'headphones')
> - See autocomplete suggestions as they type
>
> I'm on AWS. Should I use OpenSearch or stick with PostgreSQL full-text search? What's the sync strategy between my PostgreSQL database and the search index?"

**For architecture:**
> "We're adding Elasticsearch to our app. Currently have PostgreSQL as the source of truth. What's the best way to keep them in sync? We can tolerate up to 30 seconds of delay. Worried about handling failures during indexing."

**For relevance tuning:**
> "Our search results aren't great - exact title matches don't appear first, and popular products get buried. Using Elasticsearch with default scoring. How do we boost title matches and factor in product popularity?"

---

## Quick Check

<details>
<summary><b>What's an inverted index?</b></summary>

A mapping from words to documents containing them. The opposite of a document listing its words. Enables fast lookup of "which documents contain this word."

</details>

<details>
<summary><b>Why use a separate search engine instead of SQL LIKE?</b></summary>

Performance (inverted index vs table scan), relevance ranking, typo tolerance, stemming, synonyms. SQL LIKE doesn't scale and doesn't understand relevance.

</details>

<details>
<summary><b>Why isn't the search engine the primary database?</b></summary>

Search engines are optimized for search, not for transactions, relationships, or being the source of truth. Use them alongside your primary database.

</details>

---

Next: [Distributed Logging](09-distributed-logging.md)
