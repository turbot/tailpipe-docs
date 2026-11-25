# BLOCKED: issue 91

## Kind: design

## What I was doing
Turning on the three fleet chores for this repo (issue 91).

## What I found
- Part 1 is already done and merged: #92 removed the `dependencies` entry from `.claude/agent-fleet/agent-fleet.json`.
- A maintainer comment on the issue (after #92) says the issue stays open for the other two chores: "The security audit stays off here until it can file findings in this public repo without spelling out how to use them. Dependabot can't fix waits with it."
- The file today still has `off: true` for `security-audit`, `secret-scan` and `dependabot-cant-fix`. `secret-scan` is meant to stay off.

## Why I stopped
Removing `off` from `security-audit` and `dependabot-cant-fix` would go against that standing maintainer decision. Nothing in the repo or issue shows its condition is now met. A public security audit would file exploit-level detail in public issues, which cannot be taken back once posted.

## Next step
A maintainer says the fleet can now file audit findings in public repos safely. Then delete `off` from those two entries (leaving `secret-scan` off) and rerun this issue.
