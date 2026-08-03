# Week 3 Capstone — Secure Cloud-Hosted Health Monitoring System

## The goal
Take the Week 2 EC2 + nginx + health monitor script and make it properly secured and
durably backed up — combining IAM (Day 18), EC2 (Day 19), S3 (Day 20), and VPC/Security
Groups (Day 21) into one real, working piece of infrastructure.

## Architecture

```
EC2 instance (public subnet, Security Group locked to SSH-from-my-IP + HTTP-open)
    → cron runs monitor.sh every 15 minutes
    → script checks: nginx status, public IP, port 80, nginx error count, disk usage
    → writes results to healthlog.txt (local, on the instance)
    → script's final line uploads healthlog.txt to S3, using a least-privilege
      IAM user's credentials (configured via `aws configure`)
    → S3 bucket stores the log durably, independent of the EC2 instance's survival
```

## IAM — least privilege in practice
Created a dedicated IAM user scoped only to what this task needs — S3 upload access to
one specific bucket — rather than reusing an admin user. Generated a CLI access key for
this user specifically (Command Line Interface key type), used only on the EC2 instance
for automated uploads.

## Final working script (`monitor.sh`)
```bash
#!/bin/bash
logfile="/home/Nithish/capstone-week2/healthlog.txt"
timestamp=$(date)

if systemctl is-active --quiet nginx
then
    echo "nginx status: running" >> $logfile
else
    echo "nginx status: not running" >> $logfile
fi

ip=$(curl -sL ifconfig.me)
echo "my ip address: $ip" >> $logfile

port=$(ss -tnlp | grep ":80")
echo "portstatus: $port" >> $logfile

errorcheck=$(grep -i error /var/log/nginx/error.log | wc -l)
echo "errorcheck: $errorcheck" >> $logfile

diskcheck=$(df -h / | tail -1 | awk '{print $5}')
echo "diskcheck: $diskcheck" >> $logfile

echo "" >> $logfile

aws s3 cp $logfile s3://nithish-linux-journey-backup-2026/healthlog.txt
```

## Real bugs hit and fixed (the actual debugging story)

1. **Wrong domain, not just missing `-L`:** used `ipconfig.me` instead of `ifconfig.me` —
   `-sL` alone didn't fix the redirect message because it was hitting the wrong domain to
   begin with.

2. **Root vs normal-user home directories:** running the script with `sudo bash
   monitor.sh` meant the AWS CLI looked for credentials at `/root/.aws/credentials`, not
   `/home/Nithish/.aws/credentials` where `aws configure` had actually saved them —
   `Unable to locate credentials` even though configuration had genuinely been done.
   Fixed by adding the user to the `adm` group (`sudo usermod -aG adm Nithish`) so the
   script could read `/var/log/nginx/error.log` without needing `sudo` at all — avoiding
   the root/normal-user credential mismatch entirely, and matching how cron would run the
   script anyway (not as root).

3. **Variable name mismatch hiding real errors:** the script calculated the real error
   count into `errorcheck`, but the `echo` line referenced an undefined `$error` variable
   and hardcoded the text "NO ERROR" — meaning the log would always claim zero errors
   regardless of the real count. A monitoring script silently lying about its own results
   is a serious class of bug. Fixed by referencing `$errorcheck` correctly.

4. **IAM permissions boundary blocking S3 upload:** got `AccessDenied ... because no
   permissions boundary allows the s3:PutObject action` — a permissions boundary is a
   separate, stricter ceiling on top of a regular IAM policy; even with an allow policy
   attached, a boundary that doesn't include an action overrides it. Resolved by
   reviewing and adjusting the boundary on the IAM user.

5. **Exposed AWS credentials:** access key and secret key were briefly visible in a
   terminal screenshot. Treated as compromised immediately — deactivated and deleted the
   key, generated a new one, re-ran `aws configure` with the replacement. A genuine
   real-world security lesson about credential hygiene, not just a theoretical one.

## Security Group hardening
- Port 22 (SSH) restricted to "My IP" only, instead of open to the entire internet
- Port 80 (HTTP) left open, since the website needs to remain publicly reachable
- Confirmed SSH access and site availability still worked correctly after the change

## Automation
```bash
crontab -e
```
```
*/15 * * * * /bin/bash /home/Nithish/capstone-week2/monitor.sh
```
Confirmed the S3 object's "Last modified" timestamp updated automatically at the next
15-minute mark, with no manual action.

## Backup
Took a fresh AMI snapshot of the instance only after confirming the full pipeline (script
+ cron + IAM + S3 upload) worked correctly — so the snapshot represents a known-good,
fully working state, not a broken intermediate one.

## Why this matters
If the EC2 instance were lost entirely — terminated by mistake, or a hardware failure —
the health check history in S3 would still exist untouched, and a new instance could be
rebuilt from the AMI snapshot. Nothing about this monitoring history depends on the
original instance surviving.

## The one-sentence interview answer
"I built a monitoring script on an EC2 instance in a public subnet, secured with a
Security Group locked to only the ports it needs. It runs on cron every 15 minutes,
checks server health, and uploads the log to S3 using a dedicated least-privilege IAM
user — not admin access. If the instance ever fails, I have an AMI snapshot to restore
from, and the logs are safely backed up outside the instance in S3."
