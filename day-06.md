# Day 6 — Redirection & Pipes (+ Process Management glimpse)

## Concepts covered
- Redirection operators: `>`, `>>`, `<`, `2>`
- Pipes: `|`
- Common pipe commands: `grep`, `sort`, `wc`, `uniq`, `cut`
- Brief intro to process management: `ps`, `htop`, background jobs with `&`, `kill`
  (full deep-dive scheduled Week 2)

## What I learned

### Redirection
- `>` — redirect output to a file, **overwrites** existing content
- `>>` — redirect output to a file, **appends** to existing content
- `<` — redirect input from a file into a command
- `2>` — redirect error output (stderr) separately from normal output (stdout)
- `2>/dev/null` — discard errors entirely

### Pipes
- `|` takes the output of one command and feeds it as input to the next,
  letting you chain commands into a pipeline instead of running them
  one at a time and copying output manually.

### Key commands used with pipes
- `grep pattern` — filter lines matching a pattern (`-i` case-insensitive,
  `-v` invert match, `-c` count matches)
- `sort` — sort lines (`-n` numeric, `-r` reverse)
- `wc -l` — count lines
- `uniq` — remove adjacent duplicate lines (use after `sort`, since `uniq`
  only catches duplicates that are next to each other)
- `cut` — extract specific columns/fields from text

## Hands-on
- Practiced overwrite vs append with `>` and `>>`
- Captured command output to a file: `ls -la /etc > etc_listing.txt`
- Redirected errors separately: `ls /nonexistent 2> errors.txt`
- First pipeline: `ls -la /etc | grep ".conf"`
- Extracted and sorted usernames: `cat /etc/passwd | cut -d: -f1 | sort`
- Counted installed packages: `dpkg -l | wc -l`
- Combined grep + wc: `cat /etc/passwd | grep "/bin/bash" | wc -l`
- Practiced `sort | uniq` pattern for removing duplicates

### Process management (glimpse only)
- `ps aux` — snapshot of all running processes
- `htop` — live, auto-updating view of processes
- `&` — run a command in the background, freeing up the terminal
- `jobs` — list background jobs
- `kill <PID>` — stop a process by its ID
- Example: `sleep 100 &` → `jobs` → `ps aux | grep sleep` → `kill <PID>`

## Why this matters
Pipes are the single most powerful concept in the shell — almost every
real troubleshooting task (searching logs, counting occurrences, filtering
output) is built from chaining these simple commands together instead of
using one complex tool.
