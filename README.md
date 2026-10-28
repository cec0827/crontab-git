# crontab-git

Treat your crontab like source code. Diff, history, atomic apply, rollback — all backed by a plain git repo you can read, grep, and push elsewhere.

No daemon. No hooks. No dependencies beyond `git`, `crontab`, and `$EDITOR`.

## Install

```bash
curl -o /usr/local/bin/crontab-git https://raw.githubusercontent.com/kithfoss/crontab-git/main/crontab-git
chmod +x /usr/local/bin/crontab-git
```

Or clone and symlink, but first prepare environment...

```bash
mkdir /home/chuck/.local/bin
echo 'export PATH="$PATH:/home/chuck/.local/bin"' >> ~/.bashrc
source ~/.bashrc
```
now clone and create the symlink

```bash
git clone https://github.com/kithfoss/crontab-git.git ~/.local/src/crontab-git
ln -s ~/.local/src/crontab-git/crontab-git ~/.local/bin/
```

## Quickstart

```bash
crontab-git init          # snapshot your existing crontab
crontab-git edit          # edit, diff, confirm, apply — all atomic
crontab-git log           # history
crontab-git diff          # is the live crontab in sync with the tracked copy?
crontab-git rollback HEAD~1   # restore previous version
```

## Why

Editing crontab with `crontab -e` is high-stakes and leaves no trail:

- Typo? Silent cron failures for days.
- Forgot what you changed last Tuesday? `man crontab` won't tell you.
- Cron edited by someone else (or another tool)? No way to know.

`crontab-git` keeps a plain git repo at `~/.config/crontab-git/`. Every edit is a commit. Drift detection catches out-of-band changes (other tools, another shell) and preserves them as "drift" commits so nothing is lost.

## Commands

| Command | What |
|---|---|
| `init` | Create the repo from current crontab |
| `edit` | Open tracked file in `$EDITOR`, diff, confirm, apply |
| `diff` | Show delta between live crontab and tracked file |
| `apply` | Install tracked file to crontab (no edit) |
| `pull` | Absorb out-of-band changes into history |
| `log [N]` | Show commits |
| `show <ref>` | Print a previous version |
| `rollback <ref>` | Restore to a previous version (as a new commit) |
| `status` | Quick summary |

## Atomic apply

Every `edit`/`apply`/`rollback` writes a tempfile, runs `crontab <tempfile>`, and only commits after a successful install. If `crontab` rejects the file (syntax error), nothing changes and git history stays clean.

## Environment

- `CRONTAB_GIT_DIR` — override repo directory (default `~/.config/crontab-git`)
- `EDITOR` / `VISUAL` — editor for `edit` (default `vi`)

## Push your crontab to a private repo

Because it's a plain git repo, you can add a remote for backup:

```bash
cd ~/.config/crontab-git
git remote add origin git@github.com:you/my-crontab-private.git
git push -u origin main
```

Now your cron schedule lives somewhere beyond the machine. Don't push to public — your jobs are your setup.

## License

MIT.
