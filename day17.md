# Day 17 — SSH Keys for GitHub Authentication

## Concepts covered
- Why SSH keys exist: public/private key pairs instead of typing a password/token
- Private key stays on the machine, never shared; public key is given to GitHub
- How this differs from what was already happening with HTTPS + Git Credential Manager
  (first push authenticates, token gets cached locally, later pushes reuse it silently —
  not the same thing as SSH, but explains why no password prompt was appearing)
- Why tokens can expire but SSH keys don't work the same way
- Direct connection to EC2: this same public/private key concept is how SSH into AWS
  instances works — practicing it here on GitHub first

## Key commands
```bash
ssh-keygen -t ed25519 -C "your_email@example.com"   # generate the key pair
cat ~/.ssh/id_ed25519.pub                            # view public key (safe to share)
ssh -T git@github.com                                # test connection to GitHub
git remote -v                                        # check current remote URL (https vs ssh)
git remote set-url origin git@github.com:username/repo.git   # switch remote to SSH
```

## What I did
- Checked for existing SSH keys with `ls -al ~/.ssh`
- Generated a new key pair with `ssh-keygen -t ed25519`
- Copied the public key with `cat ~/.ssh/id_ed25519.pub`
- Added it to GitHub under Settings → SSH and GPG keys
- Tested the connection with `ssh -T git@github.com`
- Checked existing remote with `git remote -v` and switched it to the SSH URL
- Confirmed a push worked without any password/token prompt

## Real question raised and resolved during this session
Noticed pushes were already working without a password prompt, even before switching to
SSH. Reason: HTTPS + Git Credential Manager caches the token locally after the first
authentication — that's why later pushes look password-free, but it's a cached token, not
the same mechanism as SSH keys. Switching to SSH replaces that cached-token approach with
actual key-pair authentication, which also carries forward directly to EC2 access later.

## Why this matters
Same public/private key idea will be used to SSH into EC2 instances in Month 2 — this was
the lower-stakes place to learn it first.
