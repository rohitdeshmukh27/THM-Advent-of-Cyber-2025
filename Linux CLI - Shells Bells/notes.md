# Task 1: Intro

## 🎯 Learning Objectives

- Understand basic Linux CLI commands.
- Use the CLI for navigation and system administration.
- Apply commands to uncover Christmas clues.

---

## 🔌 Connecting to the Machine

- Click **Start Machine** → split screen opens automatically.
- Use the following credentials:

```
Username: mcskidy
Password: AoC2025!
IP Address: MACHINE_IP
Connection: SSH
```

### SSH Command

```
ssh mcskidy@MACHINE_IP
```

---

# Task 2: Linux CLI

## Basic Commands

- `echo "Hello World!"` → prints text.
- `ls` → lists files in current directory.
- `cat <file>` → shows file content.
- `pwd` → shows current directory.

---

## Navigating the Filesystem

- `cd <directory>` → switch directory.
- Hidden files start with a dot (`.`).
- `ls -la` → shows **all** files including hidden ones.

---

## Hidden Guide

- Use `ls -la` to see `.guide.txt`
- View it with: `cat .guide.txt`

---

## Logs & Grep

- Logs stored in `/var/log/`
- `grep "<text>" <file>` → search inside file.
- Example:
  `grep "Failed password" auth.log`

---

## Finding Suspicious Files

- `find <path> -name <pattern>`
  Example:
  `find /home/socmas -name *egg*`

---

## Analyzing Shell Scripts

Suspicious scripts usually contain:

- `cat file | sort | uniq` → list unique entries.
- `rm file` → delete file.
- `mv file1 file2` → rename/replace files.
- Pipes (`|`), redirects (`>`, `>>`), AND operator (`&&`).

---

## CLI Symbols

| Symbol | Meaning                                      | Example                       |        |       |
| ------ | -------------------------------------------- | ----------------------------- | ------ | ----- |
| `      | `                                            | pipe output                   | `cat a | sort` |
| `>`    | overwrite output file                        | `cmd > out.txt`               |        |       |
| `>>`   | append to file                               | `cmd >> log.txt`              |        |       |
| `&&`   | run next command only if first is successful | `grep "x" file && echo found` |        |       |

---

## Useful System Commands

- `uptime` → system running time
- `ip addr` → IP address
- `ps aux` → running processes

---

## Permissions & Root

- `/etc/shadow` contains hashed passwords (root only).
- `sudo su` → switch to root.
- `whoami` → check current user.

---

## Bash History

- Every user has a hidden history file:

  - `/home/<user>/.bash_history`
  - `/root/.bash_history`

- View history:

  - `history`
  - `cat .bash_history`

---

# Objectives

Which CLI command would you use to list a directory?

<details>
  <summary>Click to reveal the answer</summary>

**The answer:**
ls

</details>
