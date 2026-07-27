# Day 15 — Git & GitHub: Repo Structure & Real Merge Conflict

## Concepts covered
- Folder organization in Git (mkdir + git mv, not a special git command)
- Git does not track empty folders
- `git log --oneline` — compact commit history view
- `git mv` vs plain `mv` (stages rename as one action instead of delete+add)
- `git add -A` vs `git add <folder>` — catching deletions, not just new files
- Diverged history between local and remote
- `git pull` = fetch + merge combined
- Resolving a push rejection caused by remote having a commit local didn't have

## What I already knew coming in
Git config, clone, remote linking, add/commit/push, basic update workflow.

## What was new

### Folder reorganization
Reorganized the whole repo from flat day-by-day files into a clean structure:
```
week1/
week2/
```
Used `git mv oldfile week1/` instead of plain `mv`, so Git staged the rename as a
single tracked action instead of showing it as a deleted file + a separate untracked file.

### git log --oneline
Used this to see the full commit history compressed to one line per commit — hash +
message only. Made it obvious which of my past commit messages were actually useful
(`day13 learning`, `day11 struggle`) versus vague ones from earlier (`updated`, `updated`,
`removed file`) that don't tell anyone what actually changed.

### Real error hit: push rejected

After committing the reorganization, `git push` failed with:
```
! [rejected]  main -> main (fetch first)
error: failed to push some refs...
hint: Updates were rejected because the remote contains work that you do not have locally.
```

**Why this happened:** GitHub had a commit that my local laptop never received (likely
from an earlier edit made directly on the GitHub website). Local and remote history had
diverged — each had a commit the other didn't have. Git blocked the push to prevent that
remote commit from being silently deleted.

**Fix:**
```bash
git pull
```
This fetched the missing commit and merged it with my local history automatically. Vim
opened asking for a merge commit message — confirmed there were no conflict markers
(`<<<<<<<`, `=======`, `>>>>>>>`), meaning the merge was automatic and clean. Saved and
exited with `:wq`, then `git push` succeeded.

## Key lesson
Before starting work on a repo that might have changes from elsewhere (browser edits,
another machine), run `git pull` first — catches a possible divergence early, instead of
hitting a rejected push after already committing.

## Result
- Repo reorganized into `week1/` and `week2/` folders
- All renames done with `git mv`, correctly staged
- Push conflict understood and resolved for real, not just copy-pasting a fix
- Clean `git log --oneline` history confirmed after merge
