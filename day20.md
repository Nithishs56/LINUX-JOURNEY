# Day 20 — S3 (Simple Storage Service)

## Concepts covered
- Object storage vs block storage (permanent fix for the TCS interview mistake)
- Buckets and objects
- Storage classes
- Public vs private access, bucket policies
- Versioning

## Why this mattered
This is the permanent, hands-on fix for calling S3 "block storage" twice in the TCS
interview — after physically using S3, that mistake shouldn't be possible again.

## Core concepts

**S3 = object storage** — stores files ("objects") inside buckets, accessed over the
internet via URL/API, not attached to any single machine. This is the core difference
from EBS (Day 19), which is a disk bolted onto one EC2 instance.

**Bucket** — the container holding objects. Bucket names must be globally unique across
all of AWS, not just one account.

**Object** — the actual file stored, identified by a key (its path/name inside the
bucket).

**Storage classes** — S3 Standard (frequent access, most expensive) → Standard-IA
(infrequent access, cheaper) → Glacier (very cheap, slow retrieval, long-term archive).

**Public access / bucket policies** — S3 buckets are private by default. Making a bucket
public requires deliberately unchecking "Block all public access" and confirming the
risk — a safeguard added after repeated real-world data leaks from accidentally public
buckets.

**Versioning** — keeps every previous version of a file instead of overwriting it,
similar in spirit to how Git keeps every commit.

## S3 vs EBS — the permanent fix
| | S3 | EBS |
|---|---|---|
| Type | Object storage | Block storage |
| Attached to | Nothing — accessed via API | ONE specific EC2 instance |
| Use case | Backups, static files, large-scale storage | OS disk / active storage for a running instance |
| Analogy | Google Drive | The hard drive inside a computer |

## Tasks completed
- Created a bucket with a globally unique name
- Left "Block all public access" checked (private)
- Uploaded a file, confirmed opening its Object URL directly gave Access Denied —
  proved the "private by default" concept for real
- Enabled Versioning on a practice bucket, uploaded a modified version of the same
  filename, confirmed two versions appeared under the object's "Versions" tab
- Tried `aws s3 ls` from the EC2 instance before CLI credentials were configured —
  observed the expected credentials error, understood why it happens

## Reasoning question answered
**Q: Why can't S3 replace EBS as a running website's main storage?**
A: An operating system needs to read/write small pieces of data constantly at low
latency, and needs storage that behaves like a real, mountable/bootable disk. S3 is
accessed remotely via API (whole-object upload/download), not as a raw block device —
you can't install or boot an OS on top of an API.
