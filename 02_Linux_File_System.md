# Linux File System

## Description

The Linux File System is a hierarchical structure used to organize and manage files and directories. Everything in Linux is organized under a single root directory represented by a forward slash (/).

Understanding the Linux File System is essential for system administration, cybersecurity, networking, and daily Linux usage.

---

# What is a File System?

A File System is a method used by an operating system to store, organize, and retrieve data.

Functions:

* Store files
* Organize directories
* Manage permissions
* Control access to data

---

# Linux File System Structure

Linux uses a hierarchical directory structure.

Example:

```text
/
├── bin
├── boot
├── dev
├── etc
├── home
├── lib
├── media
├── mnt
├── opt
├── proc
├── root
├── run
├── sbin
├── tmp
├── usr
└── var
```

All files and directories start from the root directory.

---

# Root Directory (/)

The root directory is the top-level directory in Linux.

Representation:

```text
/
```

Every file and directory exists under the root directory.

---

# /home Directory

The `/home` directory stores personal files and folders of regular users.

Example:

```text
/home/komal
```

Contents may include:

* Documents
* Downloads
* Pictures
* Projects

---

# /root Directory

The `/root` directory is the home directory of the root user.

Example:

```text
/root
```

Only administrators typically access this directory.

---

# /etc Directory

The `/etc` directory contains system configuration files.

Examples:

```text
/etc/passwd
/etc/shadow
/etc/hosts
```

These files store important system settings.

---

# /bin Directory

The `/bin` directory contains essential user commands.

Examples:

```text
ls
cp
mv
cat
pwd
```

These commands are required for basic system operation.

---

# /sbin Directory

The `/sbin` directory contains system administration commands.

Examples:

```text
fdisk
reboot
shutdown
```

These commands are generally used by administrators.

---

# /usr Directory

The `/usr` directory contains user applications and utilities.

Examples:

```text
/usr/bin
/usr/lib
/usr/share
```

Many installed programs are stored here.

---

# /var Directory

The `/var` directory stores variable data.

Examples:

```text
/var/log
/var/cache
/var/tmp
```

Common contents:

* Logs
* Caches
* Mail files

---

# /tmp Directory

The `/tmp` directory stores temporary files.

Example:

```text
/tmp
```

Temporary data may be deleted automatically by the system.

---

# /boot Directory

The `/boot` directory contains files required during system startup.

Examples:

* Kernel files
* Bootloader files

Without these files, Linux cannot boot properly.

---

# /dev Directory

The `/dev` directory contains device files.

Examples:

```text
/dev/sda
/dev/sda1
/dev/null
```

Linux treats hardware devices as files.

---

# /proc Directory

The `/proc` directory is a virtual file system containing process and system information.

Example:

```text
/proc/cpuinfo
/proc/meminfo
```

Useful for viewing system details.

---

# /opt Directory

The `/opt` directory stores optional third-party software.

Example:

```text
/opt/google
```

Many manually installed applications use this directory.

---

# /media Directory

The `/media` directory is used for removable storage devices.

Examples:

* USB drives
* External hard disks
* DVDs

---

# /mnt Directory

The `/mnt` directory is used for manually mounted file systems.

Example:

```text
/mnt/backup
```

Administrators often use it during maintenance tasks.

---

# Absolute Path

An absolute path starts from the root directory.

Example:

```text
/home/komal/Documents/file.txt
```

Absolute paths always begin with `/`.

---

# Relative Path

A relative path starts from the current working directory.

Example:

```text
Documents/file.txt
```

Relative paths do not begin with `/`.

---

# Difference Between Absolute and Relative Path

| Absolute Path    | Relative Path                 |
| ---------------- | ----------------------------- |
| Starts from root | Starts from current directory |
| Begins with /    | Does not begin with /         |
| Full path        | Partial path                  |

---

# Display Current Directory

Command:

```bash
pwd
```

Example Output:

```text
/home/komal
```

---

# List Files and Directories

Command:

```bash
ls
```

Detailed view:

```bash
ls -la
```

---

# Real-Life Example

Suppose a user stores notes in:

```text
/home/komal/Notes
```

The absolute path is:

```text
/home/komal/Notes
```

If the user is already inside `/home/komal`, the relative path is:

```text
Notes
```

---

# Summary

* Linux uses a hierarchical file system structure.
* The root directory (/) is the top-level directory.
* Important directories include `/home`, `/etc`, `/var`, `/usr`, and `/tmp`.
* Absolute paths start from the root directory.
* Relative paths start from the current directory.
* Understanding the Linux File System is essential for administration, troubleshooting, and cybersecurity.
