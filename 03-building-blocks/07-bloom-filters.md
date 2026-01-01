# Bloom Filters

A tiny data structure that can tell you "definitely no" or "probably yes", saving you from wasting time on things that don't exist.

---

## What is a Bloom Filter?

Think of a nightclub bouncer with a special guest list. When someone shows up:
- If they're NOT on the list → bouncer knows for certain, refuses entry
- If they MIGHT be on the list → bouncer checks the full list to be sure

The bouncer's shortcut list is tiny (fits in their pocket), but the actual guest list is huge (thousands of names in the office).

**That's a Bloom filter:** A small, fast check that saves you from expensive lookups when the answer is "no."

---

## The Problem (Without Bloom Filters)

You have a website with 100 million users. Someone requests:

```
GET /user/alice
```

**Question:** Does alice exist?

**The slow way:**
1. Query database: "SELECT * FROM users WHERE username = 'alice'"
2. Wait for disk read (~10ms)
3. Return result

**If alice doesn't exist:**
- You just wasted 10ms hitting the database
- If you get 1000 requests/second for non-existent users, that's 10,000ms of wasted database time per second!

**What you'd like:**
A way to instantly know "alice definitely doesn't exist, don't bother checking the database."

---

## The Solution (How Bloom Filters Work)

### Simple Example

Imagine a row of 16 light switches, all off:

```
[0][0][0][0][0][0][0][0][0][0][0][0][0][0][0][0]
 0  1  2  3  4  5  6  7  8  9  10 11 12 13 14 15
```

### Adding a Name

To add "alice":
1. Use 3 different calculations on "alice"
2. Each calculation gives you a switch number
3. Flip those switches ON

```
calc1("alice") = 3  → flip switch 3
calc2("alice") = 7  → flip switch 7
calc3("alice") = 14 → flip switch 14

[0][0][0][1][0][0][0][1][0][0][0][0][0][0][1][0]
           3           7                    14
```

### Checking a Name

To check if "bob" exists:
1. Do the same 3 calculations on "bob"
2. Check if those switches are ON
3. If ALL are ON → "probably exists"
4. If ANY are OFF → "definitely does NOT exist"

```
calc1("bob") = 5  → check switch 5 → OFF
calc2("bob") = 11 → check switch 11 → OFF  
calc3("bob") = 14 → check switch 14 → ON

One switch is OFF → "bob" definitely not added
```

### Why "Probably" Exists?

Add more names:
```
Add "alice": switches 3, 7, 14
Add "carol": switches 5, 11, 14
Add "dave": switches 2, 7, 11

[0][0][1][1][0][1][0][1][0][0][0][1][0][0][1][0]
     2  3     5     7              11       14
```

Now check "eve":
```
calc1("eve") = 3  → ON (was set by "alice")
calc2("eve") = 5  → ON (was set by "carol")
calc3("eve") = 7  → ON (was set by "dave")

All switches ON, but "eve" was never added!
```

**This is a false positive.** The Bloom filter says "probably yes" because other names happened to flip those same switches.

**But:** If ANY switch is OFF, the filter can say "definitely no" with 100% certainty.

---

## Why This is Useful

### Before Bloom Filter

1000 requests for non-existent users come in:
- Database queries: 1000 × 10ms = 10,000ms of database time
- Results: "User not found" × 1000

### With Bloom Filter

1000 requests for non-existent users come in:
- Bloom filter checks: 1000 × 0.00001ms = negligible
- Database queries: 0
- Results: "User not found" × 1000

**Database completely avoided for non-existent users!**

### Space Savings

Storing 100 million usernames:
- **Full list:** ~8 GB (80 bytes per name)
- **Bloom filter:** ~120 MB (with 1% false positive rate)

**67x smaller**, in-memory, instant lookups.

---

## The Trade-off

**What you get:**
- Tiny memory footprint
- Instant lookups
- Never wrong when it says "no"

**What you give up:**
- Sometimes wrong when it says "yes" (false positives)
- Can't delete items (standard version)
- Can't list what's inside

**When to use it:**
When false positives are acceptable and you mainly want to filter out things that definitely aren't there.

---

## Real-World Examples

### Example 1: Database Query Optimization

**Problem:** Database stores 1 billion URLs. 20% of lookups are for URLs that don't exist.

**Without Bloom filter:**
```
Request: /article/xyz
→ Query database (slow disk read)
→ "Not found"
```

**With Bloom filter:**
```
Request: /article/xyz
→ Check Bloom filter (instant, in memory)
→ "Definitely not in database"
→ Return 404, skip database entirely
```

**Used by:** Cassandra, HBase, RocksDB

### Example 2: Malicious URL Detection

**Problem:** Browser needs to check if URL is dangerous. Can't send every URL to Google servers (privacy + latency).

**Solution:**
- Browser has local Bloom filter of ~10 million malicious URLs
- Check URL in Bloom filter first
- If "definitely not malicious" → load page immediately
- If "probably malicious" → ask Google servers for confirmation

**Used by:** Google Safe Browsing

### Example 3: Cache Decision

**Problem:** You don't want to cache items that users only access once. Caching one-time items wastes memory.

**Solution:**
- First access: Add to Bloom filter, fetch data, DON'T cache
- Second access: Bloom filter says "probably seen before", NOW cache it

Only items accessed multiple times get cached.

---

## How Big Should It Be?

### The Rule of Thumb

**10 bits per item** gives you roughly **1% false positive rate**.

| Items | Bits per item | Total size | False positive rate |
|-------|---------------|------------|---------------------|
| 1 million | 10 | 1.2 MB | ~1% |
| 1 million | 15 | 1.8 MB | ~0.1% |
| 100 million | 10 | 120 MB | ~1% |
| 100 million | 15 | 180 MB | ~0.1% |

### Choosing the Rate

**1% false positive rate:**
- Good for: Cache checks, URL filtering
- Means: 1 in 100 "probably yes" answers are wrong

**0.1% false positive rate:**
- Good for: Database query optimization, critical paths
- Means: 1 in 1000 "probably yes" answers are wrong

---

## What Can Go Wrong

### Problem 1: Sizing It Too Small

You expect 1 million items, size for 1 million, but actually get 10 million.

**Result:** False positive rate skyrockets from 1% to 50%+. Filter becomes useless.

**Fix:** Oversize by 2-3x, or use "Scalable Bloom Filter" that grows automatically.

### Problem 2: Can't Delete Items

Standard Bloom filter: once you add something, it's there forever.

**Why:** Turning OFF a switch might affect other items using that switch.

**Fix:** Use "Counting Bloom Filter" - stores counts instead of on/off. Slower and bigger, but supports deletion.

### Problem 3: Trusting "Probably Yes"

**Never trust "probably yes" for destructive operations.** A false positive could cause you to act on non-existent data. Always verify with the actual data source before any destructive operation.

---

## Using Bloom Filters

**JavaScript:** Libraries like `bloom-filters` provide ready-to-use implementations. Create with expected size and desired false positive rate.

**Redis:** Built-in Bloom filter support with `BF.ADD` and `BF.EXISTS` commands. Good for distributed systems where multiple services need the same filter.

**Go, Python, Java:** All have mature Bloom filter libraries.

---

## Variants

### Counting Bloom Filter

**What it changes:** Stores counts (0, 1, 2, 3...) instead of bits (0 or 1)

**Benefit:** Can delete items (decrement count)

**Cost:** 4x more memory (4 bits per counter vs 1 bit)

**Use when:** You need to add AND remove items

### Cuckoo Filter

Modern alternative to Bloom filters:
- Supports deletion (even without counting)
- Better space efficiency at low error rates
- Slightly faster lookups

**Use for:** New projects where you might need deletion

---

## Common Patterns

### Pattern 1: Cache Guard

Before checking cache or database: check Bloom filter first. If "definitely not" → return immediately without checking slower stores.

### Pattern 2: Duplicate Detection

When processing a stream of items: add each to Bloom filter after processing. Skip items the filter says it has seen. Occasional false positives (skipping a new item) may be acceptable.

### Pattern 3: Two-Level Check

Fast path: Bloom filter says "definitely not" → return not found.
Slow path: Bloom filter says "probably yes" → verify with actual data source.

---

## What An Experienced Senior Engineer Thinks About

**Memory vs accuracy trade-off.** Doubling memory from 10 to 20 bits/item reduces false positives from 1% to 0.01%. Is that worth it for your use case?

**False positive cost.** 1% false positive means 1% of "probably yes" answers trigger unnecessary work. Calculate: false positive rate × probability of "not in set" × cost of verification. Is it worth the memory savings?

**Growth strategy.** Items in production often exceed estimates. Either overprovision by 3-5x or use scalable Bloom filters that add new filter layers when full.

**Hash function quality.** Need independent hash functions. In practice, use double hashing: `h(i) = (hash1(x) + i × hash2(x)) mod m`. Fast and works well.

**Monitoring false positive rate.** Track actual false positive rate in production vs expected. Rising rate means filter is full or hash functions colliding.

---

## When NOT to Use Bloom Filters

**When you need exact answers.** Security decisions, financial transactions, anything where "probably" isn't good enough.

**When items change frequently.** Standard Bloom filter can't delete. Counting Bloom filter loses some efficiency.

**When the set is small.** If you only have 1000 items, just store them in a hash set. Bloom filter overhead isn't worth it.

**When false positives are expensive.** If checking the actual source after false positive is very slow/costly, Bloom filter might not help.

---

## Vibe Engineering Guide

When prompting about Bloom filters:

**Too vague:**
> "Add a Bloom filter"

**Better:**
> "I have 100 million URLs in my database. About 30% of requests are for URLs that don't exist. These cause slow database queries. I want to use a Bloom filter to short-circuit queries for non-existent URLs. What size filter do I need for 1% false positive rate? How do I implement this in Node.js?"

**For troubleshooting:**
> "My Bloom filter was sized for 10 million items with 1% false positive rate, but we now have 50 million items. I'm seeing way more cache misses than expected. Is the filter the problem? Should I rebuild it larger or switch to a scalable Bloom filter?"

**For architecture:**
> "Building a deduplication system for uploaded files. Need to detect if file was uploaded before. Have 500 million files. Should I use Bloom filter? What's the false positive rate I should target? Files stored in S3, checking S3 for every upload is expensive."

---

## Quick Check

<details>
<summary><b>How does a Bloom filter work in simple terms?</b></summary>

Like a row of light switches. Adding an item flips several switches ON. Checking an item looks at those same switches. If any switch is OFF, the item definitely wasn't added. If all switches are ON, it was probably added (but other items might have flipped those same switches).

</details>

<details>
<summary><b>What's the key trade-off?</b></summary>

Memory vs accuracy. Bloom filters are tiny (67x smaller than storing actual data) but sometimes say "probably yes" when the answer is actually "no." They never have false negatives, if they say "no," it's 100% certain.

</details>

<details>
<summary><b>Why can't you delete from a Bloom filter?</b></summary>

Multiple items might flip the same switch ON. If you turn a switch OFF to delete one item, you might make other items appear absent. Counting Bloom filters solve this by counting (increment when adding, decrement when removing).

</details>

<details>
<summary><b>When is a Bloom filter most useful?</b></summary>

When you have a huge set of items and many lookups for things NOT in the set. Database query optimization, cache filtering, URL blacklists, duplicate detection. Anywhere "definitely not there" is valuable and "probably there" is acceptable.

</details>

<details>
<summary><b>What happens if you add more items than planned?</b></summary>

False positive rate increases dramatically. A filter sized for 1 million items with 1% error rate might have 50%+ error rate with 10 million items. Always oversize by 2-3x or use scalable Bloom filters.

</details>

---

Next: [Search Systems](08-search-systems.md)
