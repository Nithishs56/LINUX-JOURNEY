# Day 10 — Networking Basics

## Concepts covered
- What a network and the internet actually are
- IP address — a machine's unique address on a network
- Port — a specific "door" on that address for a specific service
- DNS — translates a human-readable domain into an IP address
- Security Group (AWS firewall) — controls which ports/traffic are allowed to reach an instance

## What I learned

### Network and internet
A network is just a group of machines that can send data to each other. The internet
is a massive network of networks — machines everywhere, all able to reach each other
using addresses and agreed-upon rules (protocols).

### IP address
Every machine on a network has a unique numeric address — this is how one machine knows
*where* to send data meant for another machine. Without an IP address, there's no way to
identify which computer, out of billions, a message is meant for.

### Port
One IP address can run many different services at once. A port is a numbered "door" on
that address, so traffic knows which specific service to go to:
- Port 80 — HTTP (nginx, unencrypted web traffic)
- Port 443 — HTTPS (encrypted web traffic)
- Port 22 — SSH (remote terminal access)

### DNS
Humans don't memorize IP addresses well, so DNS acts like a phonebook — it translates a
domain name (like `example.com`) into the actual IP address behind it. When I connected
directly using my EC2 public IP instead of a domain, I was skipping the DNS step entirely
and going straight to the address.

### Security Group (AWS firewall)
Before a request even reaches my EC2 instance, AWS checks the Security Group rules first —
it decides whether traffic on a specific port, from a specific source, is allowed in at all.
If the port isn't open in the Security Group, the request never reaches nginx, regardless of
whether nginx itself is running correctly. This is separate from Ubuntu's own firewall
(`ufw`) — the Security Group is a checkpoint outside the instance, at the network level.

## The full request flow (mental model)
1. Visitor's browser sends a request to an IP or domain
2. DNS (if a domain was used) resolves it to the real IP address
3. AWS Security Group checks if the port is allowed in
4. If allowed, the request reaches the EC2 instance's public IP on port 80
5. nginx (already running as a process) reads the file from disk and sends the response back

## Hands-on tasks (to complete and fill in below)
```bash
ip addr show                  # find private IP
curl ifconfig.me              # find public IP
sudo ss -tulnp                # confirm nginx is listening on port 80
curl -I http://localhost      # confirm 200 OK response
dig google.com                # DNS lookup example
```

- [ ] Private IP found:
- [ ] Public IP found (compared to private — different because private IP only works
      inside AWS's internal network, public IP is reachable from the internet):
- [ ] Confirmed nginx listed against port 80 in `ss -tulnp` output
- [ ] Checked AWS Security Group inbound rules and compared to what's actually listening

## Why this matters
This is the exact concept I went blank on in my TCS interview — NAT Gateway, WAF, and
Security Groups all sound different but are variations of the same idea: a checkpoint that
decides what traffic is allowed to reach a server. Understanding IP/port/DNS/Security Group
as one connected flow, instead of four separate memorized definitions, is what was missing
before.
