# OverTheWire Bandit: levels 0 to 5

Study notes on the first six Bandit levels. One entry per level: the goal, the commands I used, why they work, and what I did not know.

Passwords are not stored in this repository.

Connection (from a local terminal):

```bash
ssh bandit0@bandit.labs.overthewire.org -p 2220
```

The password for level 0 is the username itself. After that, each level gives the password for the next user (bandit1, bandit2, and so on). Always run `exit` before logging in as the next user.

## Summary

| Level | Problem | Key commands | Used help |
| --- | --- | --- | --- |
| 0 | Password in a file called readme | ls, cat | No |
| 1 | File named - (a dash) | cat ./- | No |
| 2 | File name with spaces | cat with quotes | No |
| 3 | Hidden file inside inhere | ls -a, cat | No |
| 4 | Only one human-readable file among many | file, cat | No |
| 5 | File with 3 properties inside many subdirectories | find | Yes |

## Level 0

Goal: the password is in a file called readme in the home directory.

```bash
ls
cat readme
```

`ls` lists the files and `cat` prints the content.

## Level 1

Goal: the password is in a file called - in the home directory.

```bash
ls
cat ./-
```

A bare `-` is read by `cat` as "read from standard input", not as a file name. Prefixing it with `./` turns it into a path.

## Level 2

Goal: the password is in a file whose name contains spaces.

```bash
ls
cat "spaces in this filename"
```

Without quotes, the shell splits the name into separate arguments. Quotes (or backslash escapes) keep it as one. Always check the exact name with `ls` first.

## Level 3

Goal: the password is in a hidden file inside the inhere directory.

```bash
cd ./inhere/
ls -a
cat .Hiding-From-You
```

Names that start with a dot are hidden. `ls` skips them, and `ls -a` shows them.

## Level 4

Goal: the password is in the only human-readable file inside inhere.

```bash
cd ./inhere/
ls -a
file ./*
cat ./-file07
```

The file names start with a dash, so each one needs the `./` prefix. `file` reports the content type of each file. Only one is ASCII text, and the others are binary data.

## Level 5

Goal: the password is in a file somewhere under inhere with all of these properties: human-readable, 1033 bytes in size, not executable.

```bash
cd ./inhere/
find . -type f -size 1033c ! -executable
cat <path printed by find>
```

| Filter | Meaning |
| --- | --- |
| -type f | Regular files only |
| -size 1033c | Exactly 1033 bytes (the c suffix means bytes) |
| ! -executable | Negates the executable test |

There are about 20 subdirectories with files that have spaces and leading dots in their names, so opening them one by one does not scale. `find` searches the whole tree and filters by properties.

What I did not know: how to combine several `find` tests. I looked this up externally, so this level is marked as solved with help.

Note: the level text says human-readable, which means text content. `-readable` in `find` only tests the read permission. Here the size filter already isolates a single file, and `file` can confirm that it is text.

## What to remember

- A file name that starts with a dash needs `./` (or `--`) so commands do not read it as an option.
- A name with spaces needs quotes or backslash escapes.
- `ls` hides dotfiles, so use `ls -a`.
- `file` tells you the content type, which is useful when many files look alike.
- `find` searches subdirectories and filters by type, size, and permissions, which `ls` cannot do.
- In `-size`, the suffix matters: `c` is bytes. Without a suffix, the unit is 512-byte blocks.
