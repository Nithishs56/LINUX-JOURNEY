# Day 16 — Git Branching & Pull Requests

## Concepts covered
- Why branches exist: isolating in-progress work from a stable `main`
- Creating and switching branches (`git branch`, `git checkout -b`)
- Merging a branch back into `main`
- Real merge conflicts: same file, same line, edited differently on two branches
- Pull Requests (PRs) on GitHub — reviewing changes before merging, even solo

## Key commands
```bash
git branch                      # list branches, * shows current
git checkout -b new-feature     # create and switch to a new branch in one step
git push origin new-feature     # push the branch itself (not main) to GitHub
git checkout main
git pull                        # bring merged changes back down after merging on GitHub
git branch -d new-feature       # delete the branch locally once merged
```

## What I did
- Created a new branch: `add-memory-check`
- Added a memory usage check (`free -h`) to `health_check.sh` on that branch, without
  touching `main`
- Committed the change on the branch, pushed the branch to GitHub
- Opened a Pull Request on GitHub, reviewed the diff on the "Files changed" tab
- Merged the PR on GitHub, then locally ran `git checkout main` + `git pull` to bring the
  merged change down
- Confirmed with `git log --oneline` that the memory check commit now appears in `main`
- Deleted the now-merged branch locally with `git branch -d add-memory-check`

## Why this matters
Working directly on `main` means one bad edit can break the only copy of your code that
matters. Branches let me try something — like adding a new check to the health monitor —
without risking the working script. If it doesn't work, the branch gets deleted and `main`
was never affected. Pull Requests add a review step on top of that, which is exactly how
real engineering teams (not just solo projects) manage changes.

## Notes / gaps to revisit
_(fill in here if you hit a real merge conflict, or if `free -h` output needed adjusting
in the script — document the actual command output and any error, same as Day 14's
curl -L fix)_
