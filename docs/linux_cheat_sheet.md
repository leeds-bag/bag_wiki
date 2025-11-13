# Linux Command Line Cheat Sheet

Author: Callum 

## Introduction

The Linux command line is a powerful tool for interacting with your computer. It allows you to navigate files, manage processes, and perform a wide range of tasks efficiently. Below are some essential commands and tips to get you started.

---

## How to Access the Command Line

You can access the Linux command line in several ways, depending on your operating system and preferences:

- **Terminal (Linux/macOS):** Most Linux distributions and macOS have a built-in Terminal app.
- **MobaXterm (Windows):** A popular terminal emulator for Windows that provides SSH, SFTP, and more for connecting to Linux systems.
- **Windows Subsystem for Linux (WSL):** Allows you to run a Linux environment directly on Windows. Open via the Windows Terminal or the WSL app.
- **VS Code Terminal:** Visual Studio Code has an integrated terminal that can be used for command line access on any OS.
- **PuTTY (Windows):** Lightweight SSH client for connecting to remote Linux servers.
- **Jupyter Notebooks:** Some notebooks provide a terminal tab for command line access.

Choose the method that best fits your workflow and system setup.


## Basic Navigation

| Command                | Description                                 |
|------------------------|---------------------------------------------|
| `pwd`                  | Print current working directory             |
| `ls`                   | List files and directories                  |
| `ls -l`                | List with details (long format)             |
| `ls -a`                | List all files, including hidden            |
| `cd <dir>`             | Change directory to `<dir>`                 |
| `cd ..`                | Go up one directory level                   |
| `cd ~`                 | Go to your home directory                   |
| `mkdir <dir>`          | Create a new directory                      |
| `rmdir <dir>`          | Remove an empty directory                   |
| `touch <file>`         | Create an empty file                        |
| `rm <file>`            | Remove a file                               |
| `cp <src> <dest>`      | Copy file or directory                      |
| `mv <src> <dest>`      | Move or rename file/directory               |

---

## Viewing and Editing Files

| Command                | Description                                 |
|------------------------|---------------------------------------------|
| `cat <file>`           | Display file contents                       |
| `less <file>`          | View file one page at a time                |
| `head <file>`          | Show first 10 lines of a file               |
| `tail <file>`          | Show last 10 lines of a file                |
| `nano <file>`          | Edit file with nano text editor             |
| `vim <file>`           | Edit file with vim text editor              |
| `grep 'text' <file>`   | Search for 'text' in a file                 |

---

## System Info & Management

| Command                | Description                                 |
|------------------------|---------------------------------------------|
| `whoami`               | Show current user                           |
| `date`                 | Show current date and time                  |
| `df -h`                | Show disk space usage                       |
| `du -sh <dir>`         | Show size of a directory                    |
| `free -h`              | Show memory usage                           |
| `top`                  | Show running processes                      |
| `ps aux`               | List all running processes                  |
| `kill <pid>`           | Kill process with process ID                |

---

## File Permissions

| Command                | Description                                 |
|------------------------|---------------------------------------------|
| `chmod +x <file>`      | Make file executable                        |
| `chmod 755 <file>`     | Set permissions to rwxr-xr-x                |
| `chown user:group <file>` | Change file owner and group              |

---

## Networking

| Command                | Description                                 |
|------------------------|---------------------------------------------|
| `ping <host>`          | Test network connection to host             |
| `curl <url>`           | Fetch content from a URL                    |
| `wget <url>`           | Download file from a URL                    |
| `ssh user@host`        | Connect to remote host via SSH              |

---

## Other Useful Commands

| Command                | Description                                 |
|------------------------|---------------------------------------------|
| `history`              | Show command history                        |
| `man <command>`        | Show manual for a command                   |
| `echo "text"`          | Print text to terminal                      |
| `tar -xzvf <file.tar.gz>` | Extract a tar.gz archive                 |
| `zip/unzip <file.zip>` | Compress or extract zip files               |

---

## Tips
- Use `Tab` for auto-completion.
- Use `Ctrl+C` to stop a running command.
- Use `Ctrl+R` to search command history.
- Use `&&` to chain commands (run next if previous succeeds).

---

For more, see the [GNU Core Utilities Manual](https://www.gnu.org/software/coreutils/manual/coreutils.html) or run `man <command>` in your terminal.
