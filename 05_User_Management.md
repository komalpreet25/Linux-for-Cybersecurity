# Linux User Management

Linux is a **multi-user operating system**, meaning multiple users can access and use the same system with separate accounts and permissions.

User management is important for Linux administration, access control, system security, and cybersecurity auditing.

## 1. What Is User Management?

User management is the process of creating, modifying, deleting, and controlling user accounts on a Linux system.

It helps administrators decide:

* Who can access the system.
* Which files a user can access.
* Which groups a user belongs to.
* Whether a user has administrative privileges.
* When an account should be disabled or removed.

## 2. Types of Linux Users

### Root User

The root user is the superuser with user ID `0`. It has extensive administrative privileges over the system.

Example:

```bash
whoami
```

If logged in as root, the output is:

```text
root
```

### Regular User

A regular user is an account used for normal activities and everyday work.

Example:

```text
komal
```

Regular users generally have restricted privileges compared with root.

### System User

System users are typically created to run system services and applications.

Examples include accounts used by web servers, databases, and other background services.

System accounts often have restricted login capabilities.

## 3. Important User Management Files

Linux stores account and authentication information in several files.

| File           | Purpose                                        |
| -------------- | ---------------------------------------------- |
| `/etc/passwd`  | User account information                       |
| `/etc/shadow`  | Password hashes and password-aging information |
| `/etc/group`   | Group information                              |
| `/etc/gshadow` | Protected group authentication information     |

### `/etc/passwd`

View account information:

```bash
cat /etc/passwd
```

A typical entry looks like:

```text
komal:x:1000:1000:Komal:/home/komal:/bin/bash
```

The fields represent:

```text
username:password_placeholder:UID:GID:description:home_directory:login_shell
```

The `x` generally indicates that password hash information is stored separately in `/etc/shadow`.

### `/etc/shadow`

This file contains password hashes and related password-aging information.

Check its permissions:

```bash
ls -l /etc/shadow
```

Ordinary users should not have unrestricted access to this file.

**Security note:** Never share password hashes or authentication files publicly.

### `/etc/group`

View groups:

```bash
cat /etc/group
```

A typical entry looks like:

```text
developers:x:1001:komal,alice
```

This identifies the group name, group ID, and listed group members.

## 4. User IDs and Group IDs

Linux identifies users and groups using numeric IDs.

* **UID:** User ID
* **GID:** Group ID

Check your current user and group IDs:

```bash
id
```

Example output:

```text
uid=1000(komal) gid=1000(komal) groups=1000(komal),27(sudo)
```

This indicates the current user's UID, primary GID, and supplementary groups.

Check only the username:

```bash
whoami
```

Check the current user's ID information:

```bash
id -u
id -g
```

## 5. Creating a User

The `useradd` command creates a user account.

Example:

```bash
sudo useradd -m alice
```

Here:

* `sudo` runs the command with elevated privileges.
* `useradd` creates the account.
* `-m` creates a home directory.
* `alice` is the new username.

Set a password:

```bash
sudo passwd alice
```

The user can then authenticate according to the system's authentication and access policies.

### Verify the User

```bash
id alice
```

Check the account entry:

```bash
grep '^alice:' /etc/passwd
```

## 6. Creating a User with a Specific Shell

Example:

```bash
sudo useradd -m -s /bin/bash alice
```

The `-s` option specifies the user's login shell.

To inspect the configured shell:

```bash
grep '^alice:' /etc/passwd
```

Some service accounts use a shell such as `/usr/sbin/nologin` to prevent normal interactive login.

## 7. Modifying a User

The `usermod` command modifies an existing account.

### Add a User to a Supplementary Group

```bash
sudo usermod -aG developers alice
```

Options:

* `-a` means append.
* `-G` specifies supplementary groups.

**Important:** Use `-aG` when adding a user to another group. Omitting `-a` can replace the user's existing supplementary group memberships.

Verify:

```bash
groups alice
```

### Change a User's Login Shell

```bash
sudo usermod -s /bin/bash alice
```

### Lock an Account

```bash
sudo usermod -L alice
```

This locks password-based authentication for the account. It does not necessarily disable every possible authentication method or terminate existing sessions.

### Unlock an Account

```bash
sudo usermod -U alice
```

Use account-locking commands carefully and follow your organization's access-control procedures.

## 8. Deleting a User

The `userdel` command removes a user account.

```bash
sudo userdel alice
```

To remove the user's home directory as well:

```bash
sudo userdel -r alice
```

**Caution:** The `-r` option can delete the user's home directory and mail-related files. Check for important data before using it.

## 9. Creating and Managing Groups

Groups help administrators assign permissions to multiple users.

### Create a Group

```bash
sudo groupadd developers
```

### Add a User to a Group

```bash
sudo usermod -aG developers alice
```

### View a User's Groups

```bash
groups alice
```

### View Group Details

```bash
getent group developers
```

### Delete a Group

```bash
sudo groupdel developers
```

A group generally cannot be deleted while it is the primary group of an existing user.

## 10. Understanding `sudo`

`sudo` allows an authorized user to run a command with elevated privileges, according to the system's configuration.

Example:

```bash
sudo apt update
```

This runs the package-list update command with elevated privileges.

Check your current privileges:

```bash
sudo -l
```

This displays commands the current user is permitted to run through `sudo`, subject to system policy.

### `su` vs `sudo`

| Command        | Purpose                                           |
| -------------- | ------------------------------------------------- |
| `su`           | Switch to another user account                    |
| `sudo command` | Run a particular command with elevated privileges |
| `sudo -i`      | Start a root login-style shell, if authorized     |

Use elevated privileges only when required.

## 11. Password Management

The `passwd` command manages user passwords.

Change your own password:

```bash
passwd
```

An administrator can set or reset another user's password:

```bash
sudo passwd alice
```

Check password-aging information:

```bash
sudo chage -l alice
```

The `chage` command manages password expiration and aging settings.

Example:

```bash
sudo chage -M 90 alice
```

This configures a maximum password age of 90 days. Whether such a policy is appropriate depends on the organization's security requirements.

## 12. Checking Logged-In Users

### Show Current Username

```bash
whoami
```

### Show Logged-In Sessions

```bash
who
```

### Show Users and Their Activities

```bash
w
```

### Show Login History

```bash
last
```

These commands can help administrators investigate unexpected sessions and review system access history. Their output depends on available system records and logging configuration.

## 13. Understanding the Default User Environment

Each regular user commonly has a home directory under `/home`.

Example:

```text
/home/alice
/home/komal
```

Check your home directory:

```bash
echo "$HOME"
```

Move to it:

```bash
cd ~
```

The `~` symbol represents the current user's home directory.

## 14. Least Privilege in User Management

The **Principle of Least Privilege** means granting each user only the access required for their role.

For example:

* A developer may need access to a project directory.
* A monitoring service may need read access to selected logs.
* A normal user usually does not need unrestricted root access.

Avoid sharing root credentials. Prefer individual accounts, controlled `sudo` permissions, and appropriate group membership.

## 15. Common User Management Security Risks

### Excessive Administrative Access

Giving unnecessary users administrative privileges increases the impact of a compromised account.

Review authorized `sudo` access:

```bash
sudo -l
```

### Unused Accounts

Old accounts may remain accessible after a person or service no longer needs them.

Administrators should review accounts and disable or remove them according to organizational policy.

### Weak Passwords

Weak or reused passwords can increase the risk of account compromise. Use strong, unique credentials and follow the system's authentication policy.

### Incorrect Group Membership

Membership in a privileged group can grant access to sensitive resources or administrative functions.

Check group membership:

```bash
id alice
groups alice
```

### Exposed Authentication Files

Files such as `/etc/shadow` require careful protection because they contain password hashes and related authentication data.

## 16. Practical Lab: User and Group Management

Perform this lab only on your own Linux VM or a system where you have authorization.

### Step 1: Create a Test User

```bash
sudo useradd -m labuser
```

### Step 2: Set a Password

```bash
sudo passwd labuser
```

### Step 3: Create a Group

```bash
sudo groupadd labgroup
```

### Step 4: Add the User to the Group

```bash
sudo usermod -aG labgroup labuser
```

### Step 5: Verify the User

```bash
id labuser
groups labuser
```

### Step 6: Inspect the Account

```bash
grep '^labuser:' /etc/passwd
```

### Step 7: Clean Up

After confirming that no important data is stored in the test account:

```bash
sudo userdel -r labuser
sudo groupdel labgroup
```

The group deletion will succeed only if no user still has it as their primary group.

## 17. Important User Management Commands

| Command        | Purpose                                 |
| -------------- | --------------------------------------- |
| `whoami`       | Display current username                |
| `id`           | Display UID, GID, and group memberships |
| `useradd`      | Create a user                           |
| `usermod`      | Modify a user                           |
| `userdel`      | Delete a user                           |
| `passwd`       | Set or change a password                |
| `groupadd`     | Create a group                          |
| `groupdel`     | Delete a group                          |
| `groups`       | Display group membership                |
| `getent group` | Retrieve group information              |
| `who`          | Show logged-in users                    |
| `w`            | Show logged-in users and activity       |
| `last`         | Display recorded login history          |
| `sudo -l`      | List permitted `sudo` commands          |
| `chage`        | View or manage password aging           |

## 18. Interview Questions and Answers

### Q1. What is Linux user management?

Linux user management is the process of creating, modifying, deleting, and controlling user accounts and their access privileges.

### Q2. What is the difference between a UID and a GID?

A UID identifies a user, while a GID identifies a group.

### Q3. What is the difference between `/etc/passwd` and `/etc/shadow`?

`/etc/passwd` stores basic user account information, while `/etc/shadow` stores password hashes and password-aging information.

### Q4. What is the difference between `useradd` and `usermod`?

`useradd` creates a user account, while `usermod` modifies an existing account.

### Q5. What does `sudo` do?

`sudo` allows authorized users to execute commands with elevated privileges according to system policy.

### Q6. How do you add a user to a group?

```bash
sudo usermod -aG developers alice
```

### Q7. How do you check a user's groups?

```bash
groups alice
```

### Q8. How do you lock a user account?

```bash
sudo usermod -L alice
```

This locks password-based authentication; other authentication paths may require separate controls.

### Q9. What is the root user?

The root user is the Linux superuser with UID `0` and extensive administrative privileges.

### Q10. Why should we avoid giving every user root access?

Excessive privileges increase the potential damage from mistakes, compromised accounts, and unauthorized actions.

### Q11. How would you investigate a suspicious login?

I would review logged-in sessions with `who` and `w`, inspect recorded login history with `last`, and check relevant authentication logs and account permissions.

## 19. Quick Revision

```text
UID              → User ID
GID              → Group ID
/etc/passwd      → User account information
/etc/shadow      → Password hashes and aging information
/etc/group       → Group information
useradd          → Create user
usermod          → Modify user
userdel          → Delete user
groupadd         → Create group
passwd           → Manage passwords
sudo             → Run authorized commands with elevated privileges
```

## 20. Summary

Linux user management provides control over accounts, groups, authentication, and administrative privileges.

For cybersecurity, focus on:

* Understanding UID and GID.
* Knowing the purpose of `/etc/passwd` and `/etc/shadow`.
* Creating and managing users and groups.
* Using `sudo` responsibly.
* Reviewing account permissions and group memberships.
* Identifying unused accounts and excessive privileges.
* Applying the Principle of Least Privilege.

**Key takeaway:** Secure user management ensures that the right people and services have only the access they need.
