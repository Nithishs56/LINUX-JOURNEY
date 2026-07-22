# Day 11 — Shell Scripting (Introduction)

## Concepts covered
- Shebang line (`#!/bin/bash`)
- Variables
- Conditionals (`if` / `else`)
- Checking a service's status inside a script
- Running a script with `chmod +x` and `./`
- Reading user input with `read`

## Honest note on this day

This was the first day this week where I genuinely struggled. Days 1-10 were mostly
about running single commands and seeing an immediate result — this was my first real
step into actual programming logic: variables, conditions, and things executing in a
specific order based on rules. It felt harder than everything before it, and I made
several mistakes while writing my first script. I'm documenting the real mistakes below
instead of just the clean final version, because that's a more honest record of what I
actually learned.

## What I attempted first (with errors)

```bash
#!/bin/bash
systemctl status nginx
if status=running
    echo "server is up"
else
    echo "server is down"
fi

logfile="var/log/nginx/error.log"
grep -i error | wc -i $logfile
```

## Errors I made and the corrections

### 1. Comparing service status incorrectly
`systemctl status nginx` prints a whole block of text — you can't compare that directly
to the word "running" with `if status=running`. The correct approach uses a command built
specifically to give a yes/no answer:
```bash
if systemctl is-active --quiet nginx; then
    echo "Server is UP"
else
    echo "Server is DOWN"
fi
```

### 2. Missing leading slash in the file path
```bash
logfile="var/log/nginx/error.log"      # wrong — missing leading /
LOGFILE="/var/log/nginx/error.log"     # correct
```
Without the leading `/`, Linux looks for a folder called `var` inside my current
directory instead of the real system path.

### 3. Wrong flag and wrong argument order in the grep/wc line
```bash
grep -i error | wc -i $logfile      # wrong
grep -i error $LOGFILE | wc -l      # correct
```
- The file to search belongs right after `grep`, not after `wc`
- `wc -i` isn't a real flag — line counting uses `wc -l` (lowercase L)

## Final corrected script

```bash
#!/bin/bash

# Check if nginx is running
if systemctl is-active --quiet nginx; then
    echo "Server is UP"
else
    echo "Server is DOWN"
fi

# Count errors in the nginx log
LOGFILE="/var/log/nginx/error.log"
ERROR_COUNT=$(grep -i error $LOGFILE | wc -l)
echo "Number of errors found: $ERROR_COUNT"
```

## Running the script
```bash
chmod +x server_check.sh
./server_check.sh
```
I initially didn't know that a script has to be run with `./` in front of it — without
that, Linux searches other system folders for the command and doesn't find it there.

## Why this matters
Shell scripting is the foundation of automation — instead of manually running the same
audit commands from my Day 7 capstone one at a time, I can now run them all with a single
command. It's also my first real exposure to programming concepts (variables,
conditionals) that will matter again later with CI/CD pipelines and Infrastructure as
Code tools.

## Reflection
Struggling with a new concept after a week of things clicking quickly is normal, not a
sign of falling behind. Every one of my mistakes above was small and fixable once pointed
out — the difficulty was in the shift to logic-based thinking, not in any single command
being hard. Continuing into Day 12 to slow down and practice the fundamentals
(variables, arguments, comparisons, loops) separately before combining them again.
