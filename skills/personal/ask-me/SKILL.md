---
name: ask-me
description: Ask how my personal fork of Matt Pocock's skills is set up, how to change a skill, take his updates, or send a change back to him.
disable-model-invocation: true
---

# Ask Me

You forgot how this was set up, so ask.

Facts below were true when written (2026-10-17). Before stating one as current, check it with the commands in **Check the setup**, and say so if something has drifted. Read this file's answer for the situation the user describes, not the whole file.

## The setup in one paragraph

The skills come from `mattpocock/skills`. They are not installed from a registry any more. I forked the repo to `cmbryan/skills`, cloned it to `~/Documents/skills`, and symlinked every skill into `~/.claude/skills` and `~/.agents/skills` with the repo's own `scripts/link-skills.sh`. Those two folders are global to my user, so every project on this machine uses the fork. The clone is always checked out on a branch called `mine`, which is Matt's latest main plus my changes. A `git pull` or rebase in the clone updates the live skills at once, because they are symlinks.

## Where things live

| Thing | Location |
| --- | --- |
| My fork on GitHub | `https://github.com/cmbryan/skills` (remote `origin`) |
| Matt's repo | `https://github.com/mattpocock/skills` (remote `upstream`) |
| The clone my skills point at | `~/Documents/skills`, always on branch `mine` |
| A second working copy for change branches | `~/Documents/skills-work` (a git worktree of the clone) |
| Live skill folders (symlinks into the clone) | `~/.claude/skills`, `~/.agents/skills` |
| Backup of the old installer-managed install | `~/Documents/skills-backup-20261017` |
| This skill | `skills/personal/ask-me` inside the clone, on `mine` only |

## Branches

- **`mine`**: Matt's main plus my changes. The only branch the live skills ever see. Never edit or commit on it directly, not even for mine-only skills like this one: build every change on a branch in `skills-work` and merge it in.
- **One branch per change**, cut from `upstream/main`, for example `implement-spec-human-tickets`. Each one stays a clean pull request to Matt, with no unrelated edits in it. Merge each into `mine` when I want to use it.
- **`main`**: a plain copy of Matt's main. Not used day to day.

My changes so far, beyond Matt's code:
- `implement-spec` understands `ready-for-human` tickets: it never implements or closes them, tells me when one unlocks, and pauses when every remaining agent ticket waits on one. Branch `implement-spec-human-tickets`. Not yet offered to Matt.
- `ask-me`, mine only, on branch `ask-me`. It is never sent to Matt.

## How to change a skill

1. Work in `~/Documents/skills-work`, never in the clone, so the live skills stay stable while I experiment.
2. Make a branch from Matt's latest main:

```bash
git -C ~/Documents/skills fetch upstream
git -C ~/Documents/skills-work switch -c my-change upstream/main
```

3. Edit, then follow the repo's contribution rules in its `CLAUDE.md` (a changeset in `.changeset/`, a docs page re-sync under `docs/`, update `ask-matt` if a skill's place in the flows changes, and no em-dashes in prose).
4. Commit, push the branch to my fork, then merge it into `mine`:

```bash
git -C ~/Documents/skills-work push -u origin my-change
git -C ~/Documents/skills merge --no-ff my-change
git -C ~/Documents/skills push origin mine
```

The new text is live straight away through the symlinks. If I added a brand new skill, re-run `scripts/link-skills.sh` so it gets linked.

## How to take Matt's updates

```bash
git -C ~/Documents/skills fetch upstream
git -C ~/Documents/skills rebase upstream/main
git -C ~/Documents/skills push --force-with-lease origin mine
```

If the rebase conflicts, it is on a file both of us changed, probably `implement-spec`. Resolve it keeping Matt's wording and my additions. Re-run `scripts/link-skills.sh` if skills were added or renamed.

## How to send a change back to Matt

Open a pull request from the change branch (not from `mine`) on my fork to `mattpocock/skills`. GitHub shows the link after pushing the branch. Check the change against the repo's `CLAUDE.md` and `SCOPE.md` first: it expects a changeset and a docs page, and its `CLAUDE.md` says issues are judged against `SCOPE.md`. Pull requests post to someone else's repository, so I decide each one myself.

## Warnings

- **Do not run `npx skills update`.** The old installer's lock file, `~/.agents/.skill-lock.json`, still lists these skills against Matt's repo. An update may replace the symlinks with fresh copies and drop my changes. This is untested. If it happens, rebuild the links from the clone with `scripts/link-skills.sh`; nothing is lost, since my changes are in git.
- **`scripts/link-skills.sh` deletes a real folder** of the same name in `~/.claude/skills` or `~/.agents/skills` before linking. That is how the first run replaced the old copies. The backup above has the originals.
- **`misc/` skills are not linked.** `scaffold-exercises` and `migrate-to-shoehorn` are still old installer copies that no longer follow updates. So are skills that never came from Matt's repo: `chief-of-staff`, `claude-handoff`, `loop-me`, `writing-beats`, `writing-fragments`, `writing-shape` and `setup-ts-deep-modules`.
- **Branch switching changes the live skills.** Switching or editing branches in `~/Documents/skills` changes what every project sees. That is why change branches live in `skills-work`.

## Check the setup

Run these and compare with the above:

```bash
git -C ~/Documents/skills status -sb
git -C ~/Documents/skills log --oneline upstream/main..mine
readlink -f ~/.claude/skills/implement-spec
git -C ~/Documents/skills remote -v
```

- `status` should say `mine`, tracking `origin/mine`.
- `log` lists my changes on top of Matt's.
- `readlink` should end in `Documents/skills/skills/engineering/implement-spec`. If it ends in `.agents/skills/...`, the links were overwritten, so run `scripts/link-skills.sh` from the clone.

## Setting it up on a new machine

```bash
git clone https://github.com/cmbryan/skills ~/Documents/skills
git -C ~/Documents/skills remote add upstream https://github.com/mattpocock/skills.git
git -C ~/Documents/skills switch mine
bash ~/Documents/skills/scripts/link-skills.sh
```

Then add the second working copy if I want one: `git -C ~/Documents/skills worktree add ../skills-work -b scratch upstream/main`.

## Where this came from

This was set up in a session that was building a Render staging deployment for Longpress, planned with the wayfinder skill. The map is `cmbryan/longpress#50`, the spec is `cmbryan/longpress#62`, and the runbook is `docs/staging.md` in that repo. `implement-spec` was changed because that effort mixed tickets an agent can do with manual steps only I can do.
