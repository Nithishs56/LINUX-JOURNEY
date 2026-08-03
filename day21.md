# Day 21 — VPC (Virtual Private Cloud)

## Concepts covered
- VPC, subnets (public vs private)
- Internet Gateway
- Route Tables
- Security Group vs NACL
- CIDR blocks
- The full request flow tying together Days 10, 18, 19, 20, 21

## Core concepts

**VPC** — a private, isolated network inside AWS, separate from every other customer's
network.

**Subnet** — a smaller slice of a VPC's IP range.
- **Public subnet** — has a route to the internet (where the EC2 instance lives)
- **Private subnet** — no direct internet route (used for things like databases that
  shouldn't be internet-exposed)

**Internet Gateway (IGW)** — the connection point between a VPC and the actual internet.
Without an attached IGW, nothing in the VPC can reach or be reached from outside,
regardless of Security Group rules.

**Route Table** — the rules deciding where traffic from a subnet is allowed to go (e.g.
sending internet-bound traffic to the Internet Gateway).

**Security Group vs NACL**
- Security Group = firewall at the instance level, stateful (allowing traffic in
  automatically allows the response back out)
- NACL = firewall at the subnet level, stateless (in and out rules must both be set
  separately)

**CIDR block** — the range of IP addresses a VPC/subnet owns, e.g. `172.31.0.0/16`.

## The full picture (Days 10, 18, 19, 20, 21 combined)
Internet → Internet Gateway → Route Table → Subnet → Security Group → EC2 instance →
nginx. IAM decides who can configure any of this. S3 sits entirely outside this VPC
picture, accessed via public API rather than through internal networking.

## Tasks completed
- Located the Default VPC (confirmed CIDR block `172.31.0.0/16`, matching the private
  IP range seen with `ip addr` back on Day 10)
- Confirmed the instance's subnet was marked public (auto-assign public IP: yes)
- Confirmed an Internet Gateway was attached to the default VPC
- Found the route table entry sending `0.0.0.0/0` traffic to the Internet Gateway

## Reasoning question answered — and corrected
**Q: Why is a database in a private subnet more secure than one in the same public
subnet, even with identical Security Group rules?**

Initial answer given: "the Internet Gateway blocks access to the private subnet."

**Correction:** this isn't quite accurate. The Internet Gateway doesn't actively block
anything at the private subnet's door — the private subnet's **route table simply has no
route to the Internet Gateway at all**. There's no path for outside traffic to even reach
that subnet in the first place. A public subnet's route table has an explicit entry
sending internet traffic to the IGW; a private subnet's route table lacks that entry
entirely.

**Corrected one-liner:** A private subnet is more secure because there's no route to the
Internet Gateway at all — not because something is actively blocking traffic at the door.
