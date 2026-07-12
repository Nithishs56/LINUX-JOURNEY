## Day 1 - Linux Basics

It is a good day, I learned new things about Linux.

**Learned about file system**
In Linux, everything is treated as a file. Linux has a hierarchical file
system that starts with / (root directory). Linux uses built-in
directories like /etc, /logs, /dev, /tmp, /proc.

**Learned about navigation**
Commands like pwd and cd - pwd prints the current directory, cd changes
directory. Also learned mv, cp, ls, touch, mkdir.

**Hands-on practice**
- Created project-day01 with subfolders scripts, logs, configs using
  mkdir -p
- First attempt had wrong folder names (etc, log, proc instead of
  scripts, logs, configs) - fixed using mv and rmdir
- Created files with touch, copied with cp, renamed with mv,
  deleted safely with rm -i
- Used tree to visually verify folder structure
- Used ls -la to see hidden files and permissions

**User management**
- Created a new user (Nithish) using sudo adduser
- Learned difference between sudo su - Nithish (proper login shell,
  takes you to home directory) vs sudo -su Nithish (wrong, stays in
  current directory - permission denied issues)
- Gave sudo access using usermod -aG sudo Nithish
- Checked group membership with groups Nithish

**Git/GitHub**
- Learned git add only works on files inside the current repo folder
- Fixed a "pathspec did not match any files" error by moving the file
  into the correct repo directory before running git add

This is all about Day 1 learnings.

