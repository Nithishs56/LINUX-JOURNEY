# Day 19 — EC2 Deep Dive

## Concepts covered
- AMI, instance type, key pair
- EBS (block storage) vs S3 (object storage) — the exact TCS interview mistake
- Snapshots
- Vertical vs horizontal scaling — the other TCS interview mistake
- Elastic IP

## Why this mattered
Directly targets two confirmed mistakes from the TCS interview: calling S3 "block
storage," and reversing horizontal/vertical scaling.

## Core concepts

**AMI (Amazon Machine Image)** — the template an instance is built from, defining OS and
pre-installed software.

**Instance type** (e.g. `t2.micro`) — defines the hardware: CPU, RAM, network
performance.

**Key pair** — the same SSH public/private key concept already used for GitHub. AWS
generates a `.pem` file (private key) at instance launch; the matching public key is
embedded on the instance, which is why `ssh -i key.pem ubuntu@ip` works without a
password.

**EBS = block storage** — a virtual hard disk attached to ONE specific EC2 instance. The
instance's OS and files sit on an EBS volume. This is the exact concept confused with S3
in the TCS interview.

**Snapshot** — a backup/point-in-time copy of an EBS volume, stored behind the scenes in
S3.

**Vertical scaling** — making ONE instance bigger (e.g. `t2.micro` → `t2.large`). Has a
hard ceiling — eventually a single machine can't get any bigger.

**Horizontal scaling** — adding MORE instances running in parallel, typically behind a
load balancer. No hard ceiling — this is the standard real-world approach at scale.

**Elastic IP** — a fixed public IP that doesn't change when the instance is stopped and
started, unlike the default public IP which changes on every restart.

## Tasks completed
- Found instance type and EBS volume size in the EC2 console
- Created an AMI backup of the running instance (Actions → Image and templates →
  Create image)
- Allocated an Elastic IP and associated it with the instance
- Stopped and started the instance, confirmed the public IP stayed the same afterward
- SSH'd back in using the fixed IP, confirmed nginx and the cron job were still running

## Reasoning question answered
**Q: For a 10x traffic spike, scale vertically or horizontally?**
A: Horizontal — because 10x more traffic means more machines are needed to share the
load, rather than one machine growing indefinitely, which eventually hits a ceiling
anyway.

## Key one-liner
EBS is like the hard drive inside a computer — attached to one machine. S3 (Day 20) is
like Google Drive — accessible from anywhere, not tied to any one machine.
