# Object Storage

Storing files at scale, the backbone of modern data infrastructure.

---

## What is Object Storage?

Object storage stores data as objects, blobs with metadata, rather than files in a hierarchy or blocks on a disk.

**Key characteristics:**
- Flat namespace (bucket + key, no directories)
- HTTP API (GET, PUT, DELETE)
- Virtually unlimited scale
- Built-in durability and redundancy

**Examples:** Amazon S3, Google Cloud Storage, Azure Blob Storage, MinIO (self-hosted)

---

## Object Storage vs. File Storage vs. Block Storage

### Block Storage

Raw storage blocks. Server sees it as a disk.

**Examples:** AWS EBS, local SSD

**Use for:** Databases, operating systems, anything needing fast random access.

**Characteristics:**
- Lowest latency
- Limited to single attachment (usually)
- Size limited

### File Storage

Hierarchical file system over network.

**Examples:** AWS EFS, NFS, SMB

**Use for:** Shared file access, legacy applications expecting file system.

**Characteristics:**
- Familiar file/folder model
- Shared across instances
- Higher latency than block

### Object Storage

Objects with metadata in flat namespace.

**Examples:** S3, GCS, Azure Blob

**Use for:** Static files, backups, data lakes, media, logs.

**Characteristics:**
- Virtually unlimited scale
- High durability (11 nines)
- Higher latency than block/file
- HTTP API

---

## Core Concepts

### Buckets

Top-level containers. Globally unique names.

```
s3://my-app-images/
s3://my-app-backups/
s3://my-app-logs/
```

### Objects

The files you store. Identified by key (path-like string).

```
Bucket: my-app-images
Key: users/12345/profile.jpg
Object: the actual image bytes + metadata
```

### Keys

Object identifier within a bucket. Looks like a path but is actually a flat string.

```
users/12345/profile.jpg  ← This is the full key
                         ← Not directories, just a string
```

### Metadata

Key-value pairs attached to objects.

**System metadata:** Content-Type, Last-Modified, Size
**User metadata:** Custom headers (x-amz-meta-*)

---

## Access Patterns

### Direct URL Access

Objects can be accessed via HTTP URL.

```
https://my-app-images.s3.amazonaws.com/users/12345/profile.jpg
```

Public or authenticated (presigned URLs).

### Presigned URLs

Temporary access without exposing credentials.

```
Generate presigned URL:
  Bucket: my-app-images
  Key: users/12345/profile.jpg
  Expires: 15 minutes

URL: https://my-app-images.s3.amazonaws.com/users/12345/profile.jpg?
     AWSAccessKeyId=XXX&Signature=YYY&Expires=1616098800
```

**Use cases:**
- Upload directly from browser (presigned PUT)
- Download without server proxy (presigned GET)
- Time-limited access

### CDN Integration

Object storage works well with CDN.

```
User → CloudFront → S3
            ↓
       (cached at edge)
```

First request hits S3. Subsequent requests served from edge.

---

## Durability and Availability

### Durability

Probability of not losing data.

S3 Standard: 99.999999999% (11 nines)

**What this means:** If you store 10 million objects, expect to lose 1 every 10,000 years.

Achieved by replicating across multiple devices and facilities.

### Availability

Probability of being able to access data.

S3 Standard: 99.99%

**What this means:** ~53 minutes of downtime per year.

Durability vs. availability: data might be safe but temporarily inaccessible.

---

## Storage Classes

Different tiers for different access patterns.

### S3 Examples

| Class | Access | Cost | Use Case |
|-------|--------|------|----------|
| Standard | Frequent | $$$ | Active data |
| Intelligent-Tiering | Varies | $$ | Unknown patterns |
| Standard-IA | Infrequent | $$ | Backups accessed occasionally |
| Glacier | Rare | $ | Archives |
| Glacier Deep Archive | Very rare | ¢ | Compliance archives |

### Lifecycle Policies

Automatically transition objects between classes.

```
Rule: Move to Standard-IA after 30 days
Rule: Move to Glacier after 90 days
Rule: Delete after 365 days
```

Set it and forget it - objects move automatically.

---

## Performance

### Latency

Object storage has higher latency than block/file.

**First byte:** 50-200ms typical for S3

**Not suitable for:** Database storage, operating systems, real-time access.

### Throughput

Very high aggregate throughput.

S3 can handle:
- 3,500 PUT/COPY/POST/DELETE per second per prefix
- 5,500 GET/HEAD per second per prefix

**Scaling trick:** Randomize key prefixes to distribute load.

### Request Optimization

**Multipart upload:** For large files (>100MB), upload in parts in parallel.

**Byte-range fetches:** Download part of an object (e.g., first 1MB).

**Batch operations:** Process many objects in one request.

---

## Security

### Bucket Policies

JSON policies controlling access.

```json
{
  "Version": "2012-10-17",
  "Statement": [{
    "Effect": "Allow",
    "Principal": "*",
    "Action": "s3:GetObject",
    "Resource": "arn:aws:s3:::my-bucket/public/*"
  }]
}
```

### Access Control Lists (ACLs)

Legacy method. Bucket policies preferred.

### Encryption

**Server-side encryption (SSE):**
- SSE-S3: S3 manages keys
- SSE-KMS: You manage keys in KMS
- SSE-C: You provide keys

**Client-side encryption:** Encrypt before upload.

### Block Public Access

Account or bucket level settings to prevent accidental public exposure.

**Best practice:** Enable block public access by default.

---

## Common Use Cases

### Static Website Hosting

Serve HTML, CSS, JS directly from S3.

```
my-website-bucket/
  index.html
  styles.css
  app.js
  images/
    logo.png
```

Configure bucket for website hosting. Often fronted by CloudFront.

### Backup and Archive

Store database backups, log archives.

Use lifecycle policies to move to cheaper storage over time.

### Data Lake

Central repository for analytics data.

```
data-lake/
  raw/
    2024/01/15/events.parquet
  processed/
    2024/01/15/aggregates.parquet
```

Query directly with Athena, Presto, Spark.

### Media Storage

Images, videos, audio.

Store original and processed versions. Serve via CDN.

### Application Assets

User uploads, documents, attachments.

---

## Best Practices

### Key Naming

**Add randomness for performance:**

Bad: `logs/2024-01-15/app.log` (hot prefix)
Good: `abc123-logs/2024-01-15/app.log` (distributed)

**Use meaningful structure:**
```
{content-type}/{year}/{month}/{day}/{id}
images/2024/01/15/user-12345-profile.jpg
```

### Versioning

Enable versioning to protect against accidental deletion.

```
Object: report.pdf
  Version 1: created Jan 1
  Version 2: updated Jan 15 (current)
  Version 1: still exists, retrievable
```

**Cost:** Store all versions. Use lifecycle to delete old versions.

### Replication

Cross-region replication for:
- Disaster recovery
- Lower latency in other regions
- Compliance (data in specific regions)

### Cost Optimization

- Use appropriate storage classes
- Set lifecycle policies
- Delete incomplete multipart uploads
- Monitor usage with S3 analytics

---

## Common Mistakes

**Public buckets with sensitive data.** Use Block Public Access. Always.

**Not using presigned URLs.** Proxying large files through your server. Unnecessary load and cost.

**Single prefix for high traffic.** Performance throttling. Randomize prefixes.

**Not setting lifecycle policies.** Old data accumulates, costs grow.

**Ignoring cross-region for DR.** Single region = single point of failure for data.

---

## What An Experienced Senior Engineer Thinks About

**Cost at scale.** Petabytes = significant cost. Storage class optimization, lifecycle policies, data cleanup essential.

**Egress costs.** Data going out of cloud is expensive. CDN helps (cheaper egress). Consider data placement.

**Consistency model.** S3 is now strongly consistent (as of late 2020). But design for eventual consistency as best practice.

**Integration with compute.** Data lakes: query in place with Athena/Spark rather than copying data.

**Multi-cloud.** S3-compatible APIs (MinIO, GCS) allow some portability.

---

## Vibe Engineering Guide

When prompting about object storage:

**Less useful:**
> "Store files in S3"

**More useful:**
> "I'm building an image sharing app:
> - Users upload images (1-10 MB each)
> - 100K images uploaded daily
> - Images should be viewable publicly
> - Need thumbnail generation
> - Want to minimize costs for old, rarely accessed images
>
> How should I structure the S3 bucket? Should I use presigned URLs for upload? What lifecycle policies make sense?"

**For specific problems:**
> "Our S3 costs doubled last month. We have 50TB of logs. Most are old and never accessed. How can we reduce costs? What storage classes and lifecycle policies should we use?"

---

## Quick Check

<details>
<summary><b>What's the difference between object storage and file storage?</b></summary>

Object storage has a flat namespace (bucket + key), accessed via HTTP API, virtually unlimited scale. File storage has hierarchical directories, accessed via file system protocols. Object storage is better for scale; file storage for legacy apps.

</details>

<details>
<summary><b>What's a presigned URL?</b></summary>

Temporary URL that grants access to a private object without exposing credentials. Includes signature and expiration. Useful for direct browser upload/download.

</details>

<details>
<summary><b>What are storage classes for?</b></summary>

Different pricing tiers for different access patterns. Frequently accessed data pays more. Archival data pays less but with retrieval delays. Lifecycle policies auto-transition between classes.

</details>

<details>
<summary><b>Why randomize key prefixes?</b></summary>

S3 partitions by key prefix. All keys with same prefix go to same partition. High traffic to one prefix = throttling. Randomizing distributes load across partitions.

</details>

---

Next: [Level 4: Data Layer](../04-data-layer/README.md)
