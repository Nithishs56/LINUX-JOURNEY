# Day 23 — Load Balancing & Auto Scaling

## Concepts covered
- Why a load balancer is needed once there's more than one EC2 instance
- Elastic Load Balancer (ELB), Target Groups, health checks
- Auto Scaling Group (ASG), Launch Templates
- User Data scripts — code that runs automatically when a new instance launches

## Core concepts

**Load Balancer** — sits in front of multiple EC2 instances, distributes incoming
traffic across them automatically. Visitors connect to the load balancer's address, not
any instance directly.

**Health checks** — the load balancer continuously checks each instance (e.g. pinging
`/` for a 200 OK response). Unhealthy instances are automatically removed from traffic
rotation until they recover.

**Auto Scaling Group (ASG)** — automates horizontal scaling: define a rule (e.g. "CPU
above 70% → launch a new instance") and ASG handles it without manual console work.
Scales back down automatically when load drops.

**Launch Template** — the "recipe" ASG uses when creating a new instance: which AMI,
instance type, and Security Group to use. A good AMI (from Day 19 / Week 3 capstone)
means a new auto-scaled instance comes up already configured, not a blank OS.

**User Data script** — code placed in the Launch Template's Advanced settings that runs
automatically the moment a new instance launches. Used to install and start nginx
automatically, plus write a unique per-instance page (using `hostname -f`) to visually
confirm the load balancer is actually rotating between different instances.

```bash
#!/bin/bash
apt update -y
apt install nginx -y
systemctl start nginx
systemctl enable nginx
echo "<h1>Hello from $(hostname -f)</h1>" > /var/www/html/index.html
```

## Tasks completed
- Created an Application Load Balancer
- Created a Target Group, registered the existing EC2 instance
- Set health check path to `/`
- Confirmed the load balancer's DNS name served the nginx page correctly
- Created a Launch Template referencing the Week 3 AMI
- Created an Auto Scaling Group (min 1, desired 1, max 3) linked to the Launch Template
  and Target Group
- Added a target-tracking scaling policy at 70% average CPU

## Real issue hit: Target Group showed "unhealthy"

Diagnosed by checking the Target Group's Targets tab and reading AWS's specific health
check failure reason, rather than guessing. Worked through the three most likely causes:

1. **Security Group not allowing the load balancer to reach the instance** — the load
   balancer is a separate resource with its own path to the instance; port 80 needs to be
   genuinely reachable from it, not just "open" in general
2. **Health check path not actually returning 200 OK** — verified by opening the
   instance's IP directly in a browser
3. **nginx not actually running on the instance** — verified with
   `sudo systemctl status nginx`

_(Note: mark which of the three was the actual root cause once confirmed, and add the
fix that resolved it.)_

## Reasoning question
**Q: What does Multi-AZ RDS (Day 22) protect against, versus what an Auto Scaling Group
protects against?**
Multi-AZ RDS protects against a single database instance failing — an automatic standby
takes over. An Auto Scaling Group protects against **capacity** problems — not enough
instances to handle current traffic — by adding or removing EC2 instances based on load,
and also removes unhealthy instances from rotation via the load balancer's health checks.

## Key lesson
Debugging "unhealthy" target groups is a genuinely common real-world task — the fix is
almost never guesswork, it's reading the specific failure reason AWS gives you in the
Target Group's health status details, then checking that specific cause directly.
