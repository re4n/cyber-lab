# Day 01: environment setup

## Done

- Ubuntu VM `cyberlab` running with Multipass on macOS.
- Old VM `dnslab` removed; only `cyberlab` kept.
- Repo folder shared with the VM through a mount.
- Git configured and first commit made.

## Commands

On the Mac:

```bash
multipass launch --name cyberlab --cpus 2 --memory 4G --disk 20G
multipass shell cyberlab
multipass delete dnslab
multipass purge
multipass mount ~/cyber/cyber-lab cyberlab:/home/ubuntu/cyber
```

Inside the VM (prompt `ubuntu@cyberlab:~$`):

```bash
whoami
hostname
exit
```

Git:

```bash
git config --global user.name "YOUR NAME"
git config --global user.email "YOUR_NOREPLY_EMAIL"
git config --global init.defaultBranch main
git add notes-day-01.md
git commit -m "Add day 01 notes: environment setup"
```

## Lessons

- `whoami` shows the user (`ubuntu`), `hostname` shows the machine (`cyberlab`). The prompt tells which terminal I am in.
- `multipass delete` can be undone with `recover`. `multipass purge` permanently erases all deleted VMs.
- In a mounted folder, changes on either side show up on both. An `rm` there deletes real files on the Mac.
- `git add` only stages, `git commit` records locally, and only `git push` sends to GitHub.

## Problem and fix

`multipass` hung on "Waiting for daemon" (the client could not reach `multipassd`).

Order I followed, cheapest to costliest:

1. Check the versions (`multipass version`).
2. Read the daemon log.
3. Restart the service with `launchctl`.
4. Update (`brew upgrade --cask multipass`).
5. Reboot the Mac.
6. Reinstall, never with `--zap`: it wipes the VMs.

It worked after these steps, but I don't know which one fixed it, so the root cause is still unknown.
