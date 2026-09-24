# Linux Permissions and Ownership

Linux uses a **permission and ownership system** to control who can read, modify, or execute files and directories.

Understanding permissions is essential for **Linux administration, system security, privilege management, and cybersecurity**.

---

## 1. What Are Linux Permissions?

Linux permissions determine what different users can do with a file or directory.

The three basic permissions are:

| Permission | Symbol | Meaning                              |
| ---------- | ------ | ------------------------------------ |
| Read       | `r`    | View/read the contents               |
| Write      | `w`    | Modify the contents                  |
| Execute    | `x`    | Execute a file or access a directory |

Example:

```bash
-rwxr-xr--
```

This permission string tells us who can read, write, or execute the file.

---

# 2. Linux Ownership

Every Linux file and directory has an owner and a group.

There are three permission categories:

### User / Owner

The user who owns the file.

Represented by:

```text
u
```

### Group

Users who belong to the file's group.

Represented by:

```text
g
```

### Others

All other users on the system.

Represented by:

```text
o
```

Example:

```bash
-rwxr-xr-- 1 komal users 1200 Sep 24 script.sh
```

Here:

```text
Owner = komal
Group = users
```

---

# 3. Understanding `ls -l`

Use:

```bash
ls -l
```

Example output:

```text
-rwxr-xr-- 1 komal users 1200 Sep 24 script.sh
```

Breakdown:

```text
- rwx r-x r--
  │  │   │
  │  │   └── Others
  │  └────── Group
  └───────── Owner
```

The first character represents the file type.

| Symbol | Type          |
| ------ | ------------- |
| `-`    | Regular file  |
| `d`    | Directory     |
| `l`    | Symbolic link |

So:

```text
-rwxr-xr--
```

means:

```text
-    → regular file
rwx  → owner permissions
r-x  → group permissions
r--  → others permissions
```

---

# 4. Read, Write and Execute

## Read `r`

For a file:

```text
r = read file contents
```

Example:

```bash
cat file.txt
```

For a directory:

```text
r = list directory contents
```

---

## Write `w`

For a file:

```text
w = modify file contents
```

For a directory:

```text
w = create, delete, or rename files inside the directory
```

---

## Execute `x`

For a file:

```text
x = execute the file
```

Example:

```bash
./script.sh
```

For a directory:

```text
x = enter/access the directory
```

Example:

```bash
cd /var/log
```

---

# 5. Permission Representation

Linux permissions can be represented symbolically:

```text
rwxr-xr--
```

Divide them into three groups:

```text
rwx | r-x | r--
 ↓     ↓     ↓
user  group  others
```

Therefore:

```text
User   = rwx
Group  = r-x
Others = r--
```

---

# 6. Numeric Permission System

Linux also represents permissions using numbers.

| Permission  | Value |
| ----------- | ----: |
| Read `r`    |     4 |
| Write `w`   |     2 |
| Execute `x` |     1 |

Add the values together.

### Read + Write

```text
4 + 2 = 6
```

So:

```text
rw- = 6
```

### Read + Execute

```text
4 + 1 = 5
```

So:

```text
r-x = 5
```

### Read + Write + Execute

```text
4 + 2 + 1 = 7
```

So:

```text
rwx = 7
```

---

# 7. Common Numeric Permissions

| Numeric | Symbolic    | Meaning                                |
| ------: | ----------- | -------------------------------------- |
|   `777` | `rwxrwxrwx` | Everyone can read, write, execute      |
|   `755` | `rwxr-xr-x` | Owner full access, others read/execute |
|   `700` | `rwx------` | Only owner has access                  |
|   `644` | `rw-r--r--` | Owner read/write, others read          |
|   `600` | `rw-------` | Owner read/write only                  |
|   `444` | `r--r--r--` | Everyone can only read                 |

For example:

```bash
chmod 755 script.sh
```

means:

```text
Owner  = 7 = rwx
Group  = 5 = r-x
Others = 5 = r-x
```

---

# 8. Changing Permissions with `chmod`

`chmod` means:

**change mode**

It is used to change file and directory permissions.

Syntax:

```bash
chmod [permissions] filename
```

Example:

```bash
chmod 755 script.sh
```

---

# 9. Symbolic `chmod`

Instead of numbers, we can use:

```text
u = user
g = group
o = others
a = all
```

Examples:

### Add execute permission for owner

```bash
chmod u+x script.sh
```

### Remove write permission from group

```bash
chmod g-w file.txt
```

### Add read permission for others

```bash
chmod o+r file.txt
```

### Give execute permission to everyone

```bash
chmod a+x script.sh
```

---

# 10. Changing Ownership with `chown`

`chown` means:

**change owner**

It changes the owner of a file or directory.

Syntax:

```bash
chown user filename
```

Example:

```bash
sudo chown alice report.txt
```

Now `alice` becomes the owner of `report.txt`.

---

## Change Owner and Group

```bash
sudo chown alice:developers report.txt
```

This changes:

```text
Owner = alice
Group = developers
```

---

# 11. Changing Group with `chgrp`

`chgrp` means:

**change group**

Example:

```bash
sudo chgrp developers report.txt
```

Now the file belongs to the `developers` group.

Check with:

```bash
ls -l report.txt
```

---

# 12. Understanding `umask`

`umask` controls the **default permissions assigned when new files and directories are created**.

Check the current value:

```bash
umask
```

Example:

```text
0022
```

A common default is:

```text
0022
```

This helps prevent newly created files from automatically becoming writable by everyone.

---

# 13. File Permissions vs Directory Permissions

Permissions behave slightly differently for files and directories.

### File

```text
r → read contents
w → modify contents
x → execute
```

### Directory

```text
r → list contents
w → create/delete/rename entries
x → access/traverse the directory
```

This distinction is important when troubleshooting access problems.

---

# 14. Example: Private File

Suppose you have:

```bash
passwords.txt
```

You may want only the owner to access it.

Use:

```bash
chmod 600 passwords.txt
```

Result:

```text
-rw-------
```

Meaning:

```text
Owner  → read + write
Group  → no access
Others → no access
```

This is much safer than:

```bash
chmod 777 passwords.txt
```

---

# 15. Why `777` Can Be Dangerous

This command:

```bash
chmod 777 file
```

gives:

```text
Owner  → read/write/execute
Group  → read/write/execute
Others → read/write/execute
```

This can allow unauthorized users to modify or execute the file.

Therefore:

> Avoid using `777` unless there is a specific and justified reason.

Use the **least privilege principle** instead.

---

# 16. Principle of Least Privilege

The **Principle of Least Privilege** means:

> Give users and processes only the permissions they need to perform their required tasks.

For example, if a user only needs to read a file:

```text
r--
```

There is no reason to give:

```text
rwx
```

Least privilege reduces the potential impact of unauthorized access.

---

# 17. SUID

**SUID (Set User ID)** is a special permission applied to executable files.

When an SUID executable is run, it runs with the permissions of the **file owner** rather than the user executing it.

Example permission:

```text
-rwsr-xr-x
```

Notice:

```text
s
```

instead of:

```text
x
```

SUID can be useful for legitimate system operations, but incorrectly configured SUID programs can create security risks.

To find SUID files for security auditing:

```bash
find / -perm -4000 -type f 2>/dev/null
```

---

# 18. SGID

**SGID (Set Group ID)** is another special permission.

For executable files, SGID can cause the program to run with the permissions of its group.

For directories, SGID causes newly created files and directories to inherit the directory's group.

Example:

```text
drwxrwsr-x
```

Notice:

```text
s
```

in the group permission position.

Find SGID files:

```bash
find / -perm -2000 -type f 2>/dev/null
```

---

# 19. Sticky Bit

The **sticky bit** is commonly used on shared directories.

Example:

```text
drwxrwxrwt
```

The `t` at the end indicates the sticky bit.

A common example is:

```bash
/tmp
```

The sticky bit helps prevent users from deleting or renaming files belonging to other users in a shared writable directory.

Check:

```bash
ls -ld /tmp
```

---

# 20. Setting Special Permissions

### SUID

Numeric value:

```text
4000
```

Example:

```bash
chmod 4755 program
```

### SGID

Numeric value:

```text
2000
```

Example:

```bash
chmod 2755 shared-directory
```

### Sticky Bit

Numeric value:

```text
1000
```

Example:

```bash
chmod 1777 shared-directory
```

These permissions should be used carefully because they affect privilege and access behavior.

---

# 21. Checking File Ownership and Permissions

Use:

```bash
ls -l
```

For a specific file:

```bash
ls -l file.txt
```

Detailed metadata:

```bash
stat file.txt
```

Example:

```bash
stat script.sh
```

This can show:

* File size
* Owner
* Group
* Permissions
* Access time
* Modification time
* Change time

---

# 22. Practical Permission Lab

Create a test directory:

```bash
mkdir permission-lab
cd permission-lab
```

Create a file:

```bash
touch secret.txt
```

Check permissions:

```bash
ls -l secret.txt
```

Change permissions:

```bash
chmod 600 secret.txt
```

Check again:

```bash
ls -l secret.txt
```

You should see something similar to:

```text
-rw-------
```

Now make a script:

```bash
touch test.sh
```

Add execute permission:

```bash
chmod u+x test.sh
```

Check:

```bash
ls -l test.sh
```

---

# 23. Cybersecurity Relevance

Linux permissions are directly related to cybersecurity.

Security professionals use permission analysis to identify:

### 1. Sensitive files

Examples:

```text
/etc/shadow
/etc/passwd
SSH configuration
application configuration files
logs
```

### 2. Excessive permissions

For example:

```text
777
```

may allow unnecessary users to modify files.

### 3. SUID/SGID files

Security auditing can identify unusual or unnecessary privileged executables.

### 4. Incorrect ownership

A sensitive file owned by the wrong user or group can create unauthorized access.

### 5. Privilege boundaries

Permissions help determine what a normal user can and cannot access.

---

# 24. Useful Permission Commands

| Command | Purpose                                    |
| ------- | ------------------------------------------ |
| `ls -l` | View permissions and ownership             |
| `chmod` | Change permissions                         |
| `chown` | Change owner                               |
| `chgrp` | Change group                               |
| `umask` | View/change default permission mask        |
| `stat`  | View detailed file metadata                |
| `find`  | Search for files with specific permissions |

---

# 25. Common Security Checks

### Find world-writable files

```bash
find / -type f -perm -0002 2>/dev/null
```

### Find SUID files

```bash
find / -perm -4000 -type f 2>/dev/null
```

### Find SGID files

```bash
find / -perm -2000 -type f 2>/dev/null
```

These commands are useful for **security auditing and system hardening**.

---

# 26. Important Interview Questions

### Q1. What are Linux file permissions?

Linux file permissions control which users can read, modify, or execute files and directories.

---

### Q2. What are the three basic Linux permissions?

```text
r = Read
w = Write
x = Execute
```

---

### Q3. What are the three permission categories?

```text
User/Owner
Group
Others
```

---

### Q4. What does `chmod 755` mean?

```text
Owner  = rwx
Group  = r-x
Others = r-x
```

Therefore:

```text
755 = rwxr-xr-x
```

---

### Q5. What does `chmod 644` mean?

```text
Owner  = rw-
Group  = r--
Others = r--
```

Therefore:

```text
644 = rw-r--r--
```

---

### Q6. What is the difference between `chmod` and `chown`?

`chmod` changes **permissions**, while `chown` changes **ownership**.

---

### Q7. What is SUID?

SUID is a special permission that allows an executable to run with the privileges of its file owner.

---

### Q8. What is SGID?

SGID is a special permission that can make an executable run with its group privileges and causes group inheritance for files created inside an SGID directory.

---

### Q9. What is the sticky bit?

The sticky bit is commonly used on shared writable directories so users cannot delete or rename files belonging to other users.

---

### Q10. Why is `777` generally discouraged?

Because it gives read, write, and execute permissions to everyone, potentially allowing unauthorized modification or execution.

---

### Q11. What is the Principle of Least Privilege?

It means giving users and processes only the permissions necessary to perform their required tasks.

---

# 27. Quick Revision

```text
r = 4
w = 2
x = 1

User   = u
Group  = g
Others = o
```

Common permissions:

```text
600 → rw-------
644 → rw-r--r--
700 → rwx------
755 → rwxr-xr-x
777 → rwxrwxrwx
```

Important commands:

```bash
ls -l
chmod
chown
chgrp
umask
stat
find
```

Special permissions:

```text
SUID       → 4000
SGID       → 2000
Sticky Bit → 1000
```

---

# 28. Summary

Linux permissions and ownership provide the basic access-control mechanism of a Linux system.

The most important concepts are:

* User/Owner
* Group
* Others
* Read
* Write
* Execute
* `chmod`
* `chown`
* `chgrp`
* `umask`
* SUID
* SGID
* Sticky Bit
* Least Privilege

For cybersecurity, understanding permissions is essential because improper permissions can expose sensitive information, allow unauthorized modification, or create unnecessary privilege risks.
