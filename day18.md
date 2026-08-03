# Day 18 — AWS IAM (Identity and Access Management)

## Concepts covered
- Root user vs IAM user — why root should never be used for daily work
- Core building blocks: User, Group, Role, Policy
- Least Privilege — give only the minimum permissions needed, nothing extra
- MFA (Multi-Factor Authentication) — password + rotating code from an authenticator app

## Why this mattered
Two of my exact TCS interview gaps were IAM-related — I went silent on MFA and Least
Privilege. This was the day those became real, hands-on concepts instead of just terms.

## Root vs IAM user
- **Root** = the account created at signup, unlimited power over everything including
  billing. Should never be used for daily operations.
- **IAM user** = a separate identity created inside the account, limited to only the
  permissions explicitly given. Best practice: create an IAM user immediately, stop using
  root for daily work.

## Core building blocks
- **User** — one identity (a person or application)
- **Group** — a set of users sharing the same permissions
- **Role** — a temporary identity anything (a person OR an AWS service like EC2) can
  assume, without permanent credentials attached
- **Policy** — the actual JSON document defining what's allowed, attached to a user,
  group, or role

## Least Privilege
Give every user/role only the minimum permissions required for their job — nothing extra
"just in case." This limits damage if credentials are ever compromised. This concept
became very real later in Week 3, when a real permissions boundary blocked an S3 upload
because the least-privilege user genuinely didn't have more access than it needed.

## MFA
A second layer of proof beyond password — a 6-digit code from an authenticator app that
changes every 30 seconds. Even a stolen password alone isn't enough to log in.

## Tasks completed
- Created an IAM user instead of using root for daily work
- Created an `Administrators` group and attached `AdministratorAccess` to the group,
  then added the user to the group (rather than attaching the policy directly to a user)
- Enabled MFA on the IAM user using an authenticator app
- Logged out of root, logged back in using the IAM user's sign-in URL, confirmed MFA
- Reviewed (without creating) the Access Keys section under Security credentials —
  understood this is where programmatic/CLI credentials would later be generated

## Key lesson
IAM controls **who is allowed to do what** — completely separate from what's actually
installed on an instance. This distinction mattered later, when a scoped-down IAM user
was created specifically for the Week 3 capstone, separate from this admin user.
