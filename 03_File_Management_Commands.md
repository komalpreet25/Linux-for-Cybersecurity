# File Management Commands

## Description

Linux provides powerful command-line tools for creating, viewing, copying, moving, renaming, and deleting files and directories.

Understanding these commands is essential for Linux administration and cybersecurity because security professionals frequently work with configuration files, logs, scripts, tools, and evidence from the command line.

---

# 1. `pwd`

`pwd` stands for:

```text
Print Working Directory
```

It displays your current location in the Linux file system.

### Syntax

```bash
pwd
```

### Example

```bash
$ pwd
/home/komal
```

This means the current directory is `/home/komal`.

---

# 2. `ls`

`ls` is used to list files and directories.

### Basic Usage

```bash
ls
```

### Detailed Listing

```bash
ls -l
```

Shows information such as:

* Permissions
* Owner
* Group
* File size
* Modification time
* File name

### Show Hidden Files

```bash
ls -a
```

### Detailed + Hidden Files

```bash
ls -la
```

This is one of the most commonly used Linux commands.

---

# 3. `cd`

`cd` stands for:

```text
Change Directory
```

It is used to move between directories.

### Example

```bash
cd Documents
```

Move to the parent directory:

```bash
cd ..
```

Go to the user's home directory:

```bash
cd ~
```

Go directly to the root directory:

```bash
cd /
```

---

# 4. `mkdir`

`mkdir` stands for:

```text
Make Directory
```

It creates a new directory.

### Example

```bash
mkdir projects
```

Create multiple directories:

```bash
mkdir linux networking cybersecurity
```

Create nested directories:

```bash
mkdir -p project/logs/archive
```

The `-p` option creates parent directories when necessary.

---

# 5. `rmdir`

`rmdir` removes an **empty** directory.

### Example

```bash
rmdir old_folder
```

If the directory contains files, `rmdir` normally will not remove it.

For non-empty directories, `rm -r` is used.

---

# 6. `touch`

`touch` can create an empty file.

### Example

```bash
touch notes.txt
```

Create multiple files:

```bash
touch file1.txt file2.txt file3.txt
```

`touch` can also update a file's timestamps.

---

# 7. `cat`

`cat` is commonly used to display file contents.

### Example

```bash
cat notes.txt
```

It can also combine files.

```bash
cat file1.txt file2.txt
```

---

# 8. `less`

`less` is useful for viewing large files one screen at a time.

### Example

```bash
less /var/log/syslog
```

Useful controls:

```text
Space   → Next page
b       → Previous page
/word   → Search
q       → Quit
```

For large log files, `less` is often more practical than `cat`.

---

# 9. `head`

`head` displays the beginning of a file.

### Example

```bash
head notes.txt
```

By default, it displays the first 10 lines.

Show the first 20 lines:

```bash
head -n 20 notes.txt
```

---

# 10. `tail`

`tail` displays the end of a file.

### Example

```bash
tail notes.txt
```

Show the last 20 lines:

```bash
tail -n 20 notes.txt
```

### Follow a Log File

```bash
tail -f /var/log/syslog
```

The `-f` option continuously displays new lines as they are added.

This is particularly useful for monitoring logs.

---

# 11. `cp`

`cp` stands for:

```text
Copy
```

It copies files or directories.

### Copy a File

```bash
cp notes.txt backup.txt
```

### Copy a File to Another Directory

```bash
cp notes.txt ~/Documents/
```

### Copy a Directory

```bash
cp -r project backup_project
```

The `-r` option means recursive and is required when copying directories.

---

# 12. `mv`

`mv` stands for:

```text
Move
```

It is used to move or rename files and directories.

### Move a File

```bash
mv notes.txt ~/Documents/
```

### Rename a File

```bash
mv old.txt new.txt
```

### Rename a Directory

```bash
mv old_project new_project
```

Linux does not have a separate `rename` command for basic file renaming; `mv` is commonly used.

---

# 13. `rm`

`rm` stands for:

```text
Remove
```

It deletes files.

### Delete a File

```bash
rm notes.txt
```

### Delete Multiple Files

```bash
rm file1.txt file2.txt
```

### Delete a Directory and Its Contents

```bash
rm -r project
```

### Force Removal

```bash
rm -f file.txt
```

### Important Warning

Be careful with:

```bash
rm -rf
```

It can recursively delete files and directories without normal confirmation.

Always verify the path before using destructive commands, especially when working as root.

---

# 14. `file`

The `file` command identifies the type of a file.

### Example

```bash
file document.txt
```

Possible output:

```text
ASCII text
```

Another example:

```bash
file image.png
```

Possible output:

```text
PNG image data
```

This is useful when a file extension cannot be trusted.

---

# 15. `stat`

`stat` displays detailed information about a file.

### Example

```bash
stat notes.txt
```

It can show:

* File size
* Permissions
* Owner
* Inode
* Access time
* Modification time
* Change time

---

# 16. `find`

`find` searches for files and directories.

### Search by Name

```bash
find /home -name "notes.txt"
```

### Find All `.log` Files

```bash
find /var/log -name "*.log"
```

### Find Directories

```bash
find /home -type d
```

### Find Files

```bash
find /home -type f
```

`find` is extremely useful when working with large file systems.

---

# 17. `locate`

`locate` can quickly search for files using a database.

### Example

```bash
locate notes.txt
```

Unlike `find`, `locate` searches an indexed database rather than scanning the file system directly.

The database may need to be updated before newly created files appear.

---

# 18. `which`

`which` shows the location of an executable command.

### Example

```bash
which python3
```

Possible output:

```text
/usr/bin/python3
```

This helps determine which executable will be used from the current `PATH`.

---

# 19. `echo`

`echo` prints text to the terminal.

### Example

```bash
echo "Hello Linux"
```

It can also be used with output redirection.

```bash
echo "Linux Notes" > notes.txt
```

This creates or overwrites `notes.txt`.

---

# 20. Output Redirection

Linux allows command output to be redirected into files.

## `>`

Writes output to a file and overwrites existing content.

```bash
echo "Hello" > file.txt
```

## `>>`

Appends output to a file.

```bash
echo "New line" >> file.txt
```

Example:

```bash
date >> system.log
```

This adds the current date and time to the log file.

---

# 21. Input Redirection

The `<` operator can provide a file as input to a command.

Example:

```bash
sort < names.txt
```

---

# 22. Pipes `|`

A pipe sends the output of one command to another command.

Example:

```bash
ls -la | less
```

Another example:

```bash
cat access.log | grep "404"
```

This passes the contents of `access.log` to `grep`.

A more efficient version is:

```bash
grep "404" access.log
```

---

# Practical File Management Workflow

Suppose you want to create a project directory.

### Step 1 — Create Directory

```bash
mkdir cybersecurity
```

### Step 2 — Enter Directory

```bash
cd cybersecurity
```

### Step 3 — Create Files

```bash
touch notes.txt commands.txt
```

### Step 4 — Check Files

```bash
ls -l
```

### Step 5 — Add Content

```bash
echo "Linux Basics" > notes.txt
```

### Step 6 — Read Content

```bash
cat notes.txt
```

### Step 7 — Create Backup

```bash
cp notes.txt notes_backup.txt
```

### Step 8 — Rename File

```bash
mv commands.txt linux_commands.txt
```

---

# File Management in Cybersecurity

Cybersecurity professionals frequently use file management commands to:

* Examine configuration files
* Search security logs
* Organize scripts
* Analyze suspicious files
* Create backups
* Review forensic evidence
* Manage security tools

Example:

```bash
find /var/log -type f
```

This can help identify files available under the system's log directory.

---

# Important Command Summary

| Command  | Purpose                        |
| -------- | ------------------------------ |
| `pwd`    | Show current directory         |
| `ls`     | List files                     |
| `cd`     | Change directory               |
| `mkdir`  | Create directory               |
| `rmdir`  | Remove empty directory         |
| `touch`  | Create file / update timestamp |
| `cat`    | Display file contents          |
| `less`   | View large files               |
| `head`   | Show beginning of file         |
| `tail`   | Show end of file               |
| `cp`     | Copy files/directories         |
| `mv`     | Move/rename                    |
| `rm`     | Delete                         |
| `file`   | Identify file type             |
| `stat`   | Show file metadata             |
| `find`   | Search file system             |
| `locate` | Search indexed database        |
| `which`  | Locate executable              |
| `echo`   | Print text                     |
| `grep`   | Search text patterns           |

---

# Common Interview Questions

### What is the difference between `cp` and `mv`?

`cp` creates a copy, while `mv` moves or renames the original.

---

### What is the difference between `rm` and `rmdir`?

`rmdir` removes empty directories, while `rm` can remove files and, with `-r`, directories and their contents.

---

### What is the difference between `>` and `>>`?

`>` overwrites a file, while `>>` appends data to the file.

---

### What is the difference between `cat` and `less`?

`cat` displays file contents directly, while `less` allows large files to be viewed interactively page by page.

---

### What does `tail -f` do?

It continuously displays new content appended to a file, making it useful for monitoring log files.

---

### What is the purpose of `find`?

`find` searches for files and directories based on conditions such as name, type, size, and permissions.

---

### What does `-r` mean in commands such as `cp -r` and `rm -r`?

It means **recursive**, allowing the command to operate through directories and their contents.

---

# Summary

* `pwd` shows your current location.
* `ls` lists files and directories.
* `cd` changes directories.
* `mkdir` creates directories.
* `touch` creates files.
* `cp` copies files and directories.
* `mv` moves or renames files.
* `rm` deletes files and directories.
* `cat`, `less`, `head`, and `tail` are used to read files.
* `find` searches the file system.
* `>` overwrites file content, while `>>` appends content.
* Pipes allow commands to work together.
* These commands form the foundation of Linux command-line administration and cybersecurity work.
