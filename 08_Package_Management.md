# 08 — Linux Package Management

## 1. What Is Package Management?

**Package management** is the process of installing, updating, configuring, and removing software on a Linux system using package management tools.

A software package contains program files and metadata required to install and manage an application.

Package managers help administrators maintain software, resolve dependencies, and apply security updates.

### Why Is Package Management Important?

- Install cybersecurity tools and other applications.
- Update software to fix bugs and security vulnerabilities.
- Manage software dependencies.
- Remove unnecessary or outdated applications.
- Audit installed software.
- Reduce risks caused by vulnerable or untrusted packages.

---

## 2. What Is APT?

**APT (Advanced Package Tool)** is a package management system used by Debian-based Linux distributions, including:

- Kali Linux
- Parrot OS
- Debian
- Ubuntu

APT downloads packages from configured repositories and resolves dependencies during installation.

### Basic Syntax

```bash
sudo apt install package_name
```

Example:

```bash
sudo apt install nmap
```

**Cybersecurity relevance:** Security professionals use package managers to install and maintain authorized testing tools, network utilities, monitoring software, and system updates.

Package availability depends on the distribution and its configured repositories.

---

## 3. What Is a Software Repository?

A **repository** is a source that provides software packages and package metadata.

Repositories can be maintained by Linux distributions, software vendors, or organizations.

### Why Are Repositories Important?

Repositories provide a consistent way to obtain software and security updates.

Prefer official distribution repositories or other trusted sources. Installing software from unknown websites can introduce malicious or modified packages.

### Check Repository Configuration

```bash
cat /etc/apt/sources.list
```

List additional repository configuration files:

```bash
ls -la /etc/apt/sources.list.d/
```

Some modern Debian-based systems use Deb822-format `.sources` files rather than only traditional `.list` files.

**Security tip:** Do not add an unknown repository or import an unfamiliar signing key just because an installation guide tells you to.

---

## 4. Update the Package Index

Command:

```bash
sudo apt update
```

This refreshes the local package index using configured repositories.

It does **not** upgrade all installed software.

### Check Available Upgrades

```bash
apt list --upgradable
```

This shows packages for which upgrades are available according to the current package index.

### Typical Workflow

```bash
sudo apt update
apt list --upgradable
```

Updating the package index first helps APT identify the latest package versions available from configured sources.

---

## 5. Upgrade Installed Software

### Standard Upgrade

```bash
sudo apt upgrade
```

This upgrades installed packages without removing installed packages to resolve dependency changes.

### Full Upgrade

```bash
sudo apt full-upgrade
```

This can install or remove packages to resolve dependency changes.

Review the proposed changes before confirming, particularly on production systems or systems containing important tools.

### Recommended Workflow

```bash
sudo apt update
apt list --upgradable
sudo apt upgrade
```

**Note:** Kali Linux and Parrot OS are rolling-release distributions. Follow the current guidance for your distribution and review proposed package removals carefully.

---

## 6. Install Software Using APT

### Install One Package

```bash
sudo apt install nmap
```

### Install Multiple Packages

```bash
sudo apt install git curl wget
```

### Install a Network Utility

```bash
sudo apt install traceroute
```

APT resolves dependencies and downloads the required packages from configured repositories.

### Verify Installation

```bash
nmap --version
```

For Git:

```bash
git --version
```

For curl:

```bash
curl --version
```

These commands can help confirm that the relevant executable is available and report its version.

---

## 7. Search for and Inspect Packages

### Search for a Package

```bash
apt search wireshark
```

### Display Package Information

```bash
apt show wireshark
```

Package information may include its version, description, dependencies, and package source details.

### Check Package Policy and Available Versions

```bash
apt-cache policy nmap
```

This can help identify the installed version, candidate version, and configured sources associated with a package.

**Security practice:** Check the source and package identity before installing software, especially when multiple repositories are configured.

---

## 8. What Is DPKG?

**DPKG (Debian Package)** is a lower-level package management tool used by Debian-based distributions.

It manages local `.deb` packages and tracks their installation status.

APT normally handles repository downloads and dependency resolution. DPKG performs lower-level package operations.

### APT vs DPKG

| Feature | APT | DPKG |
|---|---|---|
| Main purpose | Higher-level package management | Lower-level package management |
| Downloads from repositories | Yes | No, not by itself |
| Resolves dependencies | Yes | No automatic dependency resolution |
| Installs local `.deb` files | Yes | Yes |
| Checks package status | Yes | Yes |

---

## 9. Install a Local DEB Package

If you have obtained a trusted `.deb` file, you can install it using APT:

```bash
sudo apt install ./package_name.deb
```

The `./` indicates that the package file is in the current directory.

Alternatively, use DPKG:

```bash
sudo dpkg -i package_name.deb
```

DPKG does not automatically download and resolve missing dependencies.

If installation fails because of dependency problems, inspect the error messages first. A common repair command is:

```bash
sudo apt --fix-broken install
```

Review the proposed actions before confirming.

### Security Checks

Before installing a downloaded package:

- Verify the publisher and download source.
- Check its digital signature or checksum when the publisher provides verification information.
- Avoid packages from unknown or suspicious websites.
- Do not disable package authentication to force an installation.

A checksum only proves a match to the expected value if that expected value comes from a trustworthy source.

---

## 10. Remove Installed Software

### Remove a Package

```bash
sudo apt remove package_name
```

Example:

```bash
sudo apt remove nmap
```

This removes the package while generally retaining its package-managed system-wide configuration files.

### Purge a Package

```bash
sudo apt purge package_name
```

This removes the package and its package-managed system-wide configuration files.

It does not necessarily delete user-created files, logs, or every file associated with the application.

### Remove Unneeded Dependencies

```bash
sudo apt autoremove
```

APT identifies automatically installed packages that it considers no longer required.

Always review the proposed removal list before confirming.

### Comparison

| Command | Purpose |
|---|---|
| `apt remove` | Remove a package while generally retaining system-wide configuration |
| `apt purge` | Remove a package and its package-managed configuration |
| `apt autoremove` | Remove automatically installed packages that are no longer required |

### DPKG Removal Commands

Remove a package:

```bash
sudo dpkg -r package_name
```

Purge a package:

```bash
sudo dpkg -P package_name
```

APT is generally more convenient for routine package management because it handles dependency resolution.

---

## 11. Clean the Package Cache

APT stores downloaded package archives in a local cache.

### Clean the Entire Package Archive Cache

```bash
sudo apt clean
```

This removes cached downloaded package archives.

### Remove Obsolete Cached Packages

```bash
sudo apt autoclean
```

This removes cached package files that can no longer be downloaded from configured repositories.

Cleaning the cache can free disk space, but it does not uninstall applications.

---

## 12. Check Package Status and Files

### Check a Package's Status

```bash
dpkg -s nmap
```

This displays package status and other information if the package is known to DPKG.

### List Files Installed by a Package

```bash
dpkg -L nmap
```

This lists files recorded as belonging to the installed package.

### Find Which Package Owns a File

```bash
dpkg -S /usr/bin/nmap
```

This identifies the package that owns a particular path when that path is recorded in the package database.

### List Installed Packages

```bash
dpkg -l
```

Filter the list:

```bash
dpkg -l | grep -i nmap
```

These commands are useful for system administration, software inventory, and security audits.

---

## 13. Manage Package Holds

Sometimes an administrator needs to prevent a package from being automatically upgraded.

### Place a Package on Hold

```bash
sudo apt-mark hold package_name
```

### Remove the Hold

```bash
sudo apt-mark unhold package_name
```

### List Held Packages

```bash
apt-mark showhold
```

Package holds can be useful when a known compatibility issue requires a specific version temporarily.

**Security warning:** Holding packages indefinitely can leave security vulnerabilities unpatched. Review held packages regularly and remove holds when they are no longer needed.

---

## 14. Download a Package Without Installing It

APT can download a package archive into the current directory:

```bash
apt download package_name
```

For example:

```bash
apt download tree
```

This downloads the available package archive without installing it.

Downloading a package does not mean it is safe to execute or install. Verify its source and integrity before use.

---

## 15. Package Signing and Authenticity

Linux package repositories use authentication and integrity mechanisms to help protect users from modified packages and unauthorized repository metadata.

APT verifies repository metadata according to its configured trust and signature-verification mechanisms. Package hashes referenced by authenticated metadata help establish package integrity.

### Why Does This Matter?

Without proper verification, an attacker who can influence a software source could attempt to distribute malicious packages.

### Best Practices

- Use trusted repositories.
- Keep repository configuration under administrative control.
- Review authentication and signature errors.
- Never bypass package verification just to complete an installation.
- Avoid importing untrusted signing keys.
- Do not blindly follow outdated instructions that use deprecated key-management methods.

If APT reports a signature or authentication error, investigate the repository, system clock, key configuration, and distribution instructions instead of disabling verification.

---

## 16. Troubleshooting Common APT Problems

### Problem 1: Package Not Found

Example error:

```text
E: Unable to locate package package_name
```

Possible causes:

- Incorrect package name.
- Package not available for your distribution.
- Repository configuration problem.
- Package index not updated.

Try:

```bash
sudo apt update
apt search package_name
```

Confirm the package name and whether the configured repositories provide it.

### Problem 2: Broken Dependencies

Inspect the installation error first.

A common repair command is:

```bash
sudo apt --fix-broken install
```

You can also try completing pending package configuration:

```bash
sudo dpkg --configure -a
```

Read the proposed changes and error output carefully before proceeding.

### Problem 3: Package Manager Is Locked

Example:

```text
Could not get lock
```

Another package operation may be running.

Check for active package-management processes:

```bash
ps aux | grep -E 'apt|dpkg'
```

Wait for the other operation to finish if it is active.

**Important:** Do not blindly delete lock files or terminate package-management processes. Interrupting an active installation can leave the package database in an inconsistent state.

### Problem 4: Insufficient Disk Space

Check available filesystem space:

```bash
df -h
```

Check disk usage by directory:

```bash
sudo du -sh /var/cache/apt
```

If appropriate, clean cached package archives:

```bash
sudo apt clean
```

Confirm which filesystem is full before deleting anything else.

### Problem 5: Repository Signature Error

Possible causes include:

- Incorrect system date or time.
- An expired or changed repository signing key.
- Incorrect repository configuration.
- A temporary repository problem.
- A security or authentication issue.

First, inspect the exact error and verify the repository configuration.

```bash
date
sudo apt update
```

Do not disable signature verification or allow unauthenticated packages as a workaround.

---

## 17. Package Management Logs

Linux records package-management activity in log files.

### APT History Log

```bash
sudo less /var/log/apt/history.log
```

This records APT transaction information, such as package installations, upgrades, and removals.

### DPKG Log

```bash
sudo less /var/log/dpkg.log
```

This records package-management actions performed through DPKG, including package installation, upgrades, removal, and configuration events.

### Search for Package Activity

```bash
sudo grep -i nmap /var/log/dpkg.log
```

### Search APT History

```bash
sudo grep -i nmap /var/log/apt/history.log
```

Log rotation may move older records into compressed files, and the amount of retained history depends on system configuration.

### Cybersecurity Relevance

Package logs can help an analyst investigate:

- Unexpected software installation.
- Unauthorized package removal.
- Changes made before a security incident.
- Whether a suspicious application was installed through the package manager.

Logs should be considered alongside other evidence, because they may be incomplete or altered by a privileged attacker.

---

## 18. Security Best Practices for Package Management

### 1. Keep Software Updated

Regularly review available security updates and apply them according to your system's maintenance requirements.

```bash
sudo apt update
apt list --upgradable
```

### 2. Use Trusted Repositories

Install packages from official distribution repositories or approved organizational sources whenever possible.

### 3. Avoid Unknown Installation Scripts

Be cautious with commands copied from websites, especially commands that download a script and immediately execute it with administrator privileges.

### 4. Review Package Changes

Before confirming an installation, upgrade, or removal, review the package list and proposed actions.

Unexpected removals or unfamiliar repository sources deserve investigation.

### 5. Follow Least Privilege

Use `sudo` only when administrative privileges are required.

Do not routinely run ordinary applications as root.

### 6. Audit Installed Software

```bash
dpkg -l
```

Identify unnecessary or unfamiliar software and investigate it before removal.

### 7. Investigate Authentication Errors

Do not bypass signature verification simply because a package installation fails.

### 8. Monitor Package Logs

Review APT and DPKG logs when investigating unexplained system changes.

---

## 19. Practical Lab: Install, Verify, and Remove a Package

**Objective:** Practice the basic software lifecycle using a small command-line utility.

Use your own Linux virtual machine or another system where you have authorization to administer software.

### Step 1: Refresh Package Information

```bash
sudo apt update
```

### Step 2: Search for a Utility

```bash
apt search tree
```

### Step 3: Install the Package

```bash
sudo apt install tree
```

Review the proposed installation before confirming.

### Step 4: Verify Installation

```bash
tree --version
```

### Step 5: Inspect Package Information

```bash
dpkg -s tree
```

### Step 6: List Package Files

```bash
dpkg -L tree
```

### Step 7: Review Package Ownership

```bash
dpkg -S "$(command -v tree)"
```

### Step 8: Remove the Package

```bash
sudo apt remove tree
```

### Step 9: Verify Removal

```bash
dpkg -s tree
```

The package should no longer be installed, although the exact output depends on its current state.

### What You Learned

- Refreshing repository metadata.
- Searching for a package.
- Installing software.
- Verifying package status.
- Inspecting package files.
- Finding package ownership.
- Removing software.

---

## 20. Practical Security Investigation

**Scenario:** You discover an unfamiliar application on a Linux machine and need to investigate how it is managed.

### Step 1: Check the Package Database

```bash
dpkg -l | grep -i package_name
```

Replace `package_name` with the name you are investigating.

### Step 2: Inspect Its Information

```bash
dpkg -s package_name
```

### Step 3: Check Its Package Source and Version

```bash
apt-cache policy package_name
```

### Step 4: Review Package Logs

```bash
sudo grep -i package_name /var/log/dpkg.log
```

### Step 5: Review APT History

```bash
sudo grep -i package_name /var/log/apt/history.log
```

### Step 6: Investigate Before Taking Action

Check the package's purpose, source, installed files, and surrounding system activity.

Do not assume that a package is malicious merely because its name is unfamiliar. Likewise, the absence of a package from the DPKG database does not prove that no software was installed manually.

---

## 21. Common Interview Questions and Answers

### Q1. What is package management in Linux?

Package management is the process of installing, updating, configuring, and removing software using package-management tools.

### Q2. What is APT?

APT is a higher-level package management tool used by Debian-based Linux distributions. It retrieves packages from configured repositories and resolves dependencies.

### Q3. What is DPKG?

DPKG is a lower-level tool that manages Debian packages, including installing local `.deb` files and tracking package status.

### Q4. What is the difference between `apt update` and `apt upgrade`?

`apt update` refreshes the local package index. `apt upgrade` upgrades installed packages using available package information.

### Q5. How do you install a package?

```bash
sudo apt install package_name
```

### Q6. How do you remove a package completely?

```bash
sudo apt purge package_name
```

This removes package-managed system-wide configuration files, but it does not necessarily delete every file associated with the application.

### Q7. What is the difference between APT and DPKG?

APT handles higher-level package management and dependency resolution. DPKG performs lower-level operations on Debian packages and does not automatically resolve dependencies.

### Q8. How do you check whether a package is installed?

```bash
dpkg -s package_name
```

### Q9. How do you list files belonging to a package?

```bash
dpkg -L package_name
```

### Q10. What is a Linux repository?

A repository is a software source that provides packages and package metadata.

### Q11. Why should you avoid untrusted repositories?

Untrusted repositories can distribute malicious or modified packages that may compromise system integrity and security.

### Q12. How do you investigate package installation history?

Review `/var/log/apt/history.log` and `/var/log/dpkg.log`, while accounting for log rotation and possible tampering.

### Q13. What is `apt autoremove`?

It removes automatically installed packages that APT considers no longer necessary. The proposed removal list should be reviewed first.

### Q14. How would you investigate an unfamiliar package?

Check its installed status, package information, configured source, installed files, and package logs. Correlate this information with other evidence before deciding whether the software is suspicious.

### Q15. Why is package management important in cybersecurity?

It helps security professionals install trusted tools, patch vulnerable software, audit installed applications, and investigate unexpected software changes.

---

## 22. Quick Revision Cheat Sheet

| Task | Command |
|---|---|
| Refresh package index | `sudo apt update` |
| Upgrade installed packages | `sudo apt upgrade` |
| Perform a full upgrade | `sudo apt full-upgrade` |
| Install software | `sudo apt install package_name` |
| Search packages | `apt search package_name` |
| Show package details | `apt show package_name` |
| Check package policy | `apt-cache policy package_name` |
| Check package status | `dpkg -s package_name` |
| List installed packages | `dpkg -l` |
| List package files | `dpkg -L package_name` |
| Find package owning a file | `dpkg -S /path/to/file` |
| Remove software | `sudo apt remove package_name` |
| Purge package configuration | `sudo apt purge package_name` |
| Remove unused dependencies | `sudo apt autoremove` |
| Clean package cache | `sudo apt clean` |
| Check available upgrades | `apt list --upgradable` |
| Hold a package | `sudo apt-mark hold package_name` |
| Release a package hold | `sudo apt-mark unhold package_name` |
| Read APT history | `sudo less /var/log/apt/history.log` |
| Read DPKG log | `sudo less /var/log/dpkg.log` |

---

## 23. Final Summary

Linux package management is an essential system-administration skill and an important foundation for cybersecurity.

Remember these key points:

- **APT** manages software at a higher level and resolves dependencies.
- **DPKG** manages Debian packages at a lower level.
- `apt update` refreshes package metadata; it does not upgrade installed software.
- `apt upgrade` installs available upgrades.
- `apt remove` removes software, while `apt purge` also removes package-managed system-wide configuration.
- Package repositories and signature verification help protect software integrity.
- Package logs help investigate installations, upgrades, and removals.
- Security professionals should review unfamiliar packages, use trusted repositories, and avoid bypassing package authentication.

**Next file:** `09_Networking_Commands.md` — essential Linux networking commands for cybersecurity learners.
