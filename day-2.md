## Day 2 - User Management, File Commands, and Bandit Wargame

**User management**
- Practiced adduser, and usermod -aG sudo to add a user to the sudo group
- Used groups <username> to verify group membership
- Reinforced difference between su - username (proper login shell,
  takes you to home directory with correct environment) vs -su username
  (wrong, stays in current directory, causes permission denied issues)

**File commands practiced**
- cp - copied files between folders, including renaming during copy
- mv - moved files between folders, and used it to rename files in place
- rm - deleted files safely using rm -i (asks for confirmation first)
- find - started learning this, ran into difficulty with syntax

**find command - notes to self**
Struggled with this one, so noting down the basics I found:
- find . -name "*.txt"        -> find all .txt files in current folder
- find / -type f -name "pass*" -> find files (not folders) starting with "pass"
- find . -type d              -> find only folders
- find . -perm 644             -> find files with a specific permission
- find / -size +10M            -> find files bigger than 10MB
Plan: practice find more deeply since it's heavily used in the Bandit
wargame levels for locating hidden password files.

**Bandit Wargame - Levels 0 to 5 completed**
- The game requires using Linux commands to locate passwords hidden in
  files with tricky names, permissions, or locations
- Genuinely more challenging than passive video learning - had to think
  through each level instead of just recognizing a command
- find command difficulty showed up here directly - several levels
  needed find with specific flags to locate the right file

This is all about Day 2 learnings.
