# Day 13 — Cron (Task Scheduling)

## Concepts covered
- What the cron daemon is and how it works
- Crontab file and the 5-field time syntax
- `crontab -e`, `crontab -l`, `crontab -r`
- Why full paths matter for cron (unlike running scripts manually)
- Redirecting cron output to a log file

## What I learned

### The cron daemon
Cron is a background process that checks a schedule file (the crontab) every minute and
runs anything that's due — without any manual action from me. This is the same idea as
the background/daemon processes I saw earlier in `ps aux` (like `systemd`), just applied
to my own scheduled tasks instead of system services.

### Crontab syntax
```
*  *  *  *  *  command_to_run
│  │  │  │  └── day of week (0-6, Sunday=0)
│  │  │  └───── month (1-12)
│  │  └──────── day of month (1-31)
│  └─────────── hour (0-23)
└────────────── minute (0-59)
```
`*` means "every value" for that field. `*/5` means "every 5 units" (e.g. every 5 minutes).

### Commands used
```bash
crontab -e     # edit my personal crontab
crontab -l     # list current scheduled jobs
```

## Hands-on

### Step 1 — Proved cron works with a simple test
```
* * * * * echo "Cron is working" >> /home/ubuntu/test_log.txt
```
Waited a few minutes, then checked:
```bash
cat /home/ubuntu/test_log.txt
```
Confirmed multiple lines appeared automatically, one per minute, with no manual action —
this proved cron was actually executing on schedule before trusting it with a real script.

### Step 2 — Scheduled the real script
```
*/5 * * * * /home/ubuntu/server_check.sh >> /home/ubuntu/server_check_log.txt 2>&1
```
Confirmed with `crontab -l` that the job was listed exactly as written.

### Key lesson: full paths are required
Unlike running a script manually with `./server_check.sh`, cron does not use my normal
terminal environment or current directory. The **full path**
(`/home/ubuntu/server_check.sh`) is required, or the job silently fails to find the script.

### Output redirection
`>> /home/ubuntu/server_check_log.txt 2>&1` appends both normal output and any errors to
one log file, since cron doesn't display anything on screen the way manually running a
script does — the log file is the only way to see what happened.

## Cleanup
Removed the job after confirming it worked, using `crontab -e` and deleting the line, then
verified with `crontab -l` that the crontab was empty again.

## Why this matters
Every check I've been running manually all week (nginx status, error log counts) can now
run on its own schedule, without me remembering to do it. This is the actual foundation of
DevOps automation — not just writing scripts, but having the system run them reliably on
its own.
