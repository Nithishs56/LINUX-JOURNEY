# Day 8 — User & Group Management

## Concepts covered
- Creating and deleting users: `useradd`, `userdel`
- Creating and deleting groups: `groupadd`, `groupdel`
- Adding a user to a group: `usermod -aG`
- Verifying group membership: `groups`, `/etc/group`
- Switching between user sessions: `sudo su -`
- Why home directories (`~`) are user-specific
- Principle of least privilege in practice (why a new user has no sudo rights by default)

## What I learned

### Creating a user
```bash
sudo useradd -m -s /bin/bash testdev
sudo passwd testdev
```
- `-m` creates a home directory (`/home/testdev`)
- `-s /bin/bash` sets the default shell
- Every new user automatically gets a **primary group** with the same name as the username

### Creating a group and adding a user to it
```bash
sudo groupadd devteam
sudo usermod -aG devteam testdev
```
- `-aG` is critical — `-a` means **append**. Using `-G` alone would overwrite all of a
  user's existing group memberships instead of adding one.

### Verifying group membership
```bash
groups testdev
```
Output:
```
testdev : testdev users devteam
```
- First entry = primary group
- Remaining entries = supplementary groups
- I verified this by checking **before and after** running `usermod`, not just after —
  that's the only way to actually prove the change did something.

Cross-checked with the raw group file:
```bash
cat /etc/group | grep devteam
```

## Real troubleshooting I ran into

### 1. `chown` argument order
I initially ran:
```bash
sudo chown na.txt testdev:devteam
```
This is backwards. Correct syntax is always:
```bash
chown owner:group filename
```
Fixed version:
```bash
sudo chown testdev:devteam na.txt
```

### 2. Running commands from inside the wrong user session
I used `sudo su - testdev` to switch into the `testdev` account to test things. While
inside that session, `sudo chown ...` failed with a permission error — because `testdev`
is a regular user with no sudo rights. This is **expected behavior**, not a bug: it's the
principle of least privilege in action. A newly created user should not have admin rights
unless explicitly granted.

Fix: `exit` back to my main sudo-enabled account (`ubuntu`) before running admin commands.

### 3. File not found after switching users
After creating `na.txt` while logged in as `testdev`, I switched back to `ubuntu` and got
`No such file or directory` when trying to `chown` it. The reason: `~` (home directory)
means something different depending on which user you are. The file was created in
`/home/testdev/`, not `/home/ubuntu/`. Fixed by using the full path:
```bash
sudo chown testdev:devteam /home/testdev/na.txt
ls -l /home/testdev/na.txt
```

## Cleanup
```bash
sudo userdel -r testdev    # -r also removes the home directory
sudo groupdel devteam
```
Verified both were removed:
```bash
cat /etc/passwd | grep testdev   # no output = confirmed deleted
cat /etc/group | grep devteam    # no output = confirmed deleted
```

## Why this matters
This is the exact gap that showed up in my TCS interview when I couldn't clearly explain
why a web file should be owned by `www-data` instead of my own username. Today's session
made it concrete: a process (like nginx) runs as a specific user, and that user needs
correct ownership/group membership to do its job — nothing more, nothing less. That's the
principle of least privilege, applied hands-on instead of just defined.i
