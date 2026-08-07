# Day 22 — RDS (Relational Database Service)

## Concepts covered
- Managed database service — AWS handles OS patching, backups, and maintenance
- DB Instance, Multi-AZ, Read Replicas, automated backups/snapshots
- Why a database belongs in a private subnet, not a public one
- Security Groups scoped per-resource, not shared across resource types
- Stateful Security Groups — inbound rules don't need matching outbound rules

## Core concepts

**RDS = managed database.** AWS runs and maintains the database server — OS patches,
engine updates, backups, recovery — instead of manually installing MySQL/PostgreSQL on an
EC2 instance and maintaining it by hand.

**Multi-AZ** — an automatic standby copy of the database in a different Availability
Zone, for failover if the primary fails. Directly ties back to the Availability Zone gap
from the TCS interview.

**Read Replicas** — separate copies used to handle read-heavy traffic, distinct from
Multi-AZ (which is about failure recovery, not performance).

**Private subnet placement** — a database should never be directly reachable from the
internet. Only the application layer (EC2, in the public subnet) should be able to reach
it. This is the real-world example behind the Day 21 private-subnet reasoning question.

## The two-Security-Group structure (the real struggle of the day)

Initially assumed Security Groups should be split by port type (one for SSH, one for
everything else). Corrected understanding: Security Groups are split **by resource**, not
by port type — because each resource has a different job and needs different rules.

**Security Group 1 — EC2 instance:**
- Inbound: SSH (22) from my IP only, HTTP (80) from everyone
- Outbound: left as default (allow all) — no need to manually duplicate inbound rules
  here

**Security Group 2 — RDS database:**
- Inbound: MySQL (3306), source set to **Security Group 1 itself** (referenced directly
  by typing its name/ID in the source field and selecting the autocomplete suggestion),
  not an IP address
- No SSH rule at all — nothing SSHs directly into RDS, connections happen via the MySQL
  client on port 3306 only
- Outbound: left as default

## Stateful Security Groups — real misunderstanding, corrected
Initially tried adding matching outbound rules for SSH and HTTP on the EC2 Security
Group. Corrected: Security Groups are **stateful** — if inbound traffic is allowed on a
connection, the response back out is automatically allowed, no separate outbound rule
needed. Outbound rules are only for traffic the instance **initiates** to somewhere else
(e.g. `apt update`, connecting out to RDS). Default "allow all" outbound is standard and
was left unchanged; only inbound was locked down, since inbound is the actual attack
surface.

## Verifying the database was actually private (not just assumed)
1. Confirmed **Publicly accessible = No** on the RDS Connectivity & security tab
2. Confirmed the 3306 inbound rule's source showed the EC2 Security Group ID (`sg-...`),
   not `0.0.0.0/0` or a raw IP
3. Successfully connected from the EC2 instance using the MySQL client:
   ```bash
   sudo apt install mysql-client -y
   mysql -h <rds-endpoint> -u admin -p
   SHOW DATABASES;
   ```
4. Understood the stronger proof would be attempting a connection from outside the VPC
   (e.g. from a personal laptop) and confirming it fails/times out — since there's no
   public route or allowed source, this confirms private access rather than just reading
   a setting

## Cost/cleanup decision
Reviewed free tier limits (750 hrs/month, single instance, 12-month window from account
creation) and confirmed Multi-AZ was **not** enabled (Multi-AZ is not free-tier eligible
and would incur cost). Took a final snapshot before deleting the live instance, to avoid
any billing risk while keeping the option to restore it later for a future "deploy a web
app with a real database" project.

## Key lesson
Security isn't something to assume — it's something to actively verify: check the
setting, check the rule source, and where possible, actually attempt the connection that
should fail.
