# Day 4 — Package Management (apt)

## Concepts covered
- apt update, apt upgrade, apt install, apt remove, apt purge
- Difference between update vs upgrade
- Package version syntax

## What I learned

### Correct order: update BEFORE install
`sudo apt update` refreshes the local catalog of available packages/versions 
by checking the repositories — it does NOT install or change anything on 
the system. You run this first, then install.

### apt update vs apt upgrade (commonly confused)
- `apt update`  → refreshes the package catalog only (metadata, no software changes)
- `apt upgrade` → actually upgrades already-installed packages to their 
  latest available versions
- These are NOT the same thing, despite similar names.

### Correct command sequence
sudo apt update              # refresh catalog first
apt search nginx             # find exact package name
sudo apt install nginx       # install it
nginx -v                     # confirm install + check version
sudo apt upgrade             # later, upgrades all installed packages
sudo apt remove nginx        # removes package, keeps config files
sudo apt purge nginx         # removes package AND config files

### Version-specific install (correct syntax)
apt-cache madison nginx                     # see available versions first
sudo apt install nginx=1.18.0-0ubuntu1      # package=version, no space

Note: there is no space between package name and version — it's 
package=version, joined with an equals sign. Writing "nginx 6.0" as two 
separate words is invalid syntax.

### remove vs purge
- `remove` → deletes the package, but leaves config files behind
- `purge`  → deletes the package AND its config files completely
