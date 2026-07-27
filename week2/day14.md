# Day 14 — Week 2 Revision + Capstone: Automated Server Health Monitor

## Week 2 Revision (Days 8-13)

| Day | Topic | Core commands / concepts |
|---|---|---|
| 8 | Users & Groups | `useradd -m -s /bin/bash`, `usermod -aG`, `groupadd`, `groups`, `userdel -r`, `/etc/passwd`, `/etc/group` |
| 9 | Process Management | `ps aux`, `top`/`htop`, `jobs`, `fg`, `bg`, PID persistence, `kill -15` (SIGTERM, graceful) vs `kill -9` (SIGKILL, force) vs SIGHUP (reload config) |
| 10 | Networking Basics | `ip addr` (private IP), `curl ifconfig.me` (public IP), `ss -tulnp` (ports), DNS, Security Groups vs `ufw`, full request flow: visitor → DNS → Security Group → EC2 → nginx |
| 11-12 | Shell Scripting | shebang `#!/bin/bash`, variables (no spaces around `=`), `if`/`else`/`fi`, `read -p`, `$1`/`$2` arguments, `-eq`/`-gt`/`-lt` for numbers vs `=` for text, `for`/`while` loops |
| 13 | Cron | 5-field crontab syntax, `crontab -e`/`-l`, full paths required (cron has no normal terminal environment), `>>` output redirection, `*/15` step values |

## Self-test answered before capstone
1. `kill -15` = graceful stop (lets the process clean up); `kill -9` = force kill (no cleanup, last resort)
2. Cron needs a full path because it doesn't run with a normal shell environment or current directory — a relative path or `./script.sh` won't be found
3. Security Group checks which ports/IPs are allowed to reach the EC2 instance at the AWS network level, sitting *before* traffic ever reaches the instance itself — separate from Ubuntu's own `ufw` firewall

## Capstone — Automated Server Health Monitor

Built a single shell script combining every Week 2 topic into one real, working tool,
scheduled to run on its own via cron.

**Script does:**
1. Checks nginx status (`systemctl is-active --quiet nginx`) — Day 9
2. Checks public IP (`curl -sL ifconfig.me`) and confirms port 80 listening (`ss -tulnp | grep :80`) — Day 10
3. Counts nginx error log entries (`grep -i error /var/log/nginx/error.log | wc -l`) — same logic as Day 7 capstone, now inside a script — Day 11-12
4. Checks disk usage (`df -h /`) and reports the percentage
5. Logs every check with a timestamp, appended (`>>`) to one log file
6. Scheduled with cron to run automatically every 15 minutes — Day 13

**Final script:**
```bash
#!/bin/bash

LOGFILE="/home/ubuntu/server_health_log.txt"
TIMESTAMP=$(date)

echo "----- Check run at: $TIMESTAMP -----" >> $LOGFILE

if systemctl is-active --quiet nginx; then
    echo "nginx status: running" >> $LOGFILE
else
    echo "nginx status: NOT running" >> $LOGFILE
fi

PUBLIC_IP=$(curl -sL ifconfig.me)
echo "Public IP: $PUBLIC_IP" >> $LOGFILE

PORT_CHECK=$(ss -tulnp | grep :80)
echo "Port 80 status: $PORT_CHECK" >> $LOGFILE

ERROR_COUNT=$(grep -i error /var/log/nginx/error.log | wc -l)
echo "Error count: $ERROR_COUNT" >> $LOGFILE

DISK_USAGE=$(df -h / | tail -1 | awk '{print $5}')
echo "Disk usage: $DISK_USAGE" >> $LOGFILE

echo "" >> $LOGFILE
```

Scheduled with:
```
*/15 * * * * /home/ubuntu/health_check.sh
```

## Real mistakes made and fixed
1. Shebang typo `#!bin/bash` → `#!/bin/bash` (missing leading slash)
2. `systemctl nginx active --quiet nginx` → correct is `systemctl is-active --quiet nginx`
3. Used `<<` (heredoc) instead of `>>` (append) — completely different operators
4. Missing `fi` to close the `if`/`else` block
5. Tried combining `curl` and `ss` into one command — they check two different things and needed to be separate
6. `cat var/log/nginx/error.log | grep error | wc -i` → missing leading `/`, and `wc -i` isn't a real flag (correct: `wc -l`)
7. **Real bug caught from actual output:** `curl ifconfig.me` without `-L` printed the raw "Moved Permanently" redirect message instead of the IP, because `ifconfig.me` redirects `http` to `https` and curl doesn't follow redirects by default. Fixed with `curl -sL` (`-L` = follow redirects).

## Verified output (from actual run)
```
nginx status: running
Port 80: LISTEN across nginx worker PIDs, confirmed
Error count: NO ERROR
Disk usage: 47%
```
Public IP confirmed working correctly after the `-L` fix was applied.

## Why this capstone matters
Every check that was done manually across Week 2 — nginx status, port checks, error
counts, disk space — now runs on its own, on a schedule, with no manual action. This is
the actual core idea behind DevOps automation: not just knowing commands, but building a
system that reliably checks itself.
