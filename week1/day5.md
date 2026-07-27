# Day 5 — Text Editors: nano and vim

## Concepts covered
- nano basics: open, save, exit, cut/paste, search
- vim modes: Normal, Insert, Command
- vim navigation and editing commands
- Why both matter for server work (no GUI editor on most servers)

## What I learned

### nano (beginner-friendly)
- Open a file: `nano filename.txt`
- Save: `Ctrl+O` then `Enter`
- Exit: `Ctrl+X`
- Cut a line: `Ctrl+K` / Paste: `Ctrl+U`
- Search: `Ctrl+W`

### vim has three modes — the key mental shift from nano
- **Normal mode** (default on open) — for navigation and commands, not typing
- **Insert mode** — for typing text, entered with `i`
- **Command mode** — for save/quit/search, entered with `:`

### Core vim commands
- `vim filename.txt` — open/create a file
- `i` — enter insert mode
- `Esc` — return to normal mode
- `:w` — save
- `:q` — quit
- `:wq` — save and quit
- `:q!` — quit without saving (discard changes)
- Navigation (normal mode): `h/j/k/l`, `gg` (top), `G` (bottom)
- `dd` — delete a line
- `u` — undo, `Ctrl+r` — redo
- `/searchterm` then `n` — search and jump to next match

## Hands-on
- Created and edited a file in nano, confirmed save/exit workflow
- Created a file in vim, practiced insert mode, saved with `:wq`
- Practiced navigation with `h/j/k/l` and `gg`/`G`
- Deleted a line with `dd`, undid it with `u`
- Practiced discarding unwanted changes with `:q!`
- Edited a copy of `/etc/hosts` in vim, added a comment line, saved and verified

## Why this matters
Most servers don't have a GUI text editor — vim (or at minimum nano) is the only way to edit config files directly over SSH. Interviewers often ask "are you comfortable with vim?" as a quick way to check hands-on experience versus video-only learning:.
