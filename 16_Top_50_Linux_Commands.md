
# 16. Top 50 Linux Commands for Cybersecurity

## 1. Introduction

Linux commands are essential for system administration, troubleshooting, network analysis, security monitoring, and incident response.

For cybersecurity professionals, command-line knowledge helps investigate system behavior, inspect permissions, analyze logs, identify network connections, and manage services.

This guide covers 50 useful Linux commands grouped by their purpose.

---

# Category 1: System Information Commands

## 1. `uname` – Display System Information

Displays information about the Linux kernel and system architecture.

```bash
uname -a
```

Display only the kernel version:

```bash
uname -r
```

Display machine architecture:

```bash
uname -m
```

**Cybersecurity use case:** Identify the kernel version during system inventory and vulnerability assessment.

## 2. `hostnamectl` – Display Host Information

Displays and manages hostname-related system information.

```bash
hostnamectl
```

Display the hostname:

```bash
hostname
```

**Cybersecurity use case:** Confirm the identity of a system during network investigations.

## 3. `whoami` – Identify Current User

Displays the username of the current effective user.

```bash
whoami
```

**Cybersecurity use case:** Confirm which account is executing administrative or investigation commands.

## 4. `id` – Display User and Group IDs

Displays user ID, primary group, and supplementary groups.

```bash
id
```

Check another account:

```bash
id username
```

**Cybersecurity use case:** Investigate user privileges and group memberships.

## 5. `uptime` – Check System Uptime

Displays how long the system has been running, along with load averages.

```bash
uptime
```

**Cybersecurity use case:** Establish system uptime during incident investigation and identify unexpected reboots for further investigation.

---

# Category 2: File and Directory Commands

## 6. `pwd` – Print Working Directory

Displays the current directory.

```bash
pwd
```

**Cybersecurity use case:** Verify the current location before executing administrative commands or examining files.

## 7. `ls` – List Files and Directories

Displays files and directories.

```bash
ls
```

Useful options:

```bash
ls -l
ls -la
ls -lh
ls -lt
```

- `-l`: Detailed listing.
- `-a`: Includes hidden files.
- `-h`: Human-readable file sizes.
- `-t`: Sorts by modification time.

**Cybersecurity use case:** Inspect hidden files, ownership, permissions, and recently modified files.

## 8. `cd` – Change Directory

Moves between directories.

```bash
cd /etc
```

Move to the home directory:

```bash
cd ~
```

Move to the parent directory:

```bash
cd ..
```

Return to the previous directory:

```bash
cd -
```

**Cybersecurity use case:** Navigate system directories during configuration reviews and investigations.

## 9. `mkdir` – Create Directories

Creates a directory.

```bash
mkdir security-lab
```

Create nested directories:

```bash
mkdir -p security-lab/logs/analysis
```

**Cybersecurity use case:** Organize investigation evidence, lab files, and security reports.

## 10. `find` – Search for Files

Searches for files and directories based on specified conditions.

Find a file by name:

```bash
find /etc -name "ssh_config"
```

Find files modified within the last 24 hours:

```bash
sudo find /var/log -type f -mtime -1
```

Find world-writable files on the current filesystem:

```bash
sudo find / -xdev -type f -perm -0002 -print 2>/dev/null
```

**Cybersecurity use case:** Locate configuration files, investigate recent modifications, and review potentially unsafe permissions.

---

# Category 3: File Reading and Text Processing

## 11. `cat` – Display File Contents

Displays file contents.

```bash
cat /etc/os-release
```

Combine multiple files:

```bash
cat file1.txt file2.txt
```

**Cybersecurity use case:** Quickly inspect configuration files and text-based logs.

## 12. `less` – Read Large Files

Displays file contents one screen at a time.

```bash
less /var/log/auth.log
```

Useful keys:

- `Space`: Next page.
- `b`: Previous page.
- `/keyword`: Search forward.
- `n`: Next match.
- `q`: Quit.

**Cybersecurity use case:** Analyze large logs without loading the entire file into the terminal.

## 13. `head` – Display Beginning of a File

Displays the first 10 lines by default.

```bash
head file.txt
```

Display the first 20 lines:

```bash
head -n 20 file.txt
```

**Cybersecurity use case:** Inspect the beginning of log files or configuration files.

## 14. `tail` – Display End of a File

Displays the last 10 lines by default.

```bash
tail /var/log/auth.log
```

Follow new log entries:

```bash
sudo tail -f /var/log/auth.log
```

Display the last 50 lines:

```bash
tail -n 50 file.txt
```

**Cybersecurity use case:** Monitor authentication events and application activity in real time.

## 15. `grep` – Search Text Patterns

Searches for matching text or regular expressions.

```bash
grep "Failed password" /var/log/auth.log
```

Case-insensitive search:

```bash
grep -i "error" file.txt
```

Recursive search:

```bash
grep -r "PermitRootLogin" /etc/ssh/
```

Display line numbers:

```bash
grep -n "Failed password" /var/log/auth.log
```

**Cybersecurity use case:** Find failed logins, suspicious strings, error messages, and configuration settings.

---

# Category 4: File Management and Permissions

## 16. `cp` – Copy Files

Copies files and directories.

```bash
cp file.txt backup.txt
```

Copy a directory recursively:

```bash
cp -r project/ project-backup/
```

**Cybersecurity use case:** Back up configuration files before making security changes.

## 17. `mv` – Move or Rename Files

Move a file:

```bash
mv report.txt /tmp/
```

Rename a file:

```bash
mv old.txt new.txt
```

**Cybersecurity use case:** Organize investigation files and manage configuration changes.

## 18. `rm` – Remove Files

Removes files and directories.

```bash
rm file.txt
```

Remove an empty directory:

```bash
rmdir old-directory
```

Remove a directory and its contents:

```bash
rm -r directory/
```

**Cybersecurity use case:** Remove temporary lab files or obsolete artifacts when authorized.

**Warning:** Deletion may be irreversible. Verify the path before using recursive deletion or elevated privileges. Never delete potential forensic evidence during an investigation without authorization.

## 19. `chmod` – Change File Permissions

Modifies file and directory permissions.

```bash
chmod 600 private.txt
```

Make a script executable by its owner:

```bash
chmod u+x script.sh
```

Display current permissions:

```bash
ls -l private.txt
```

**Cybersecurity use case:** Protect private keys, sensitive files, and scripts.

## 20. `chown` – Change File Ownership

Changes the owner and group of files.

```bash
sudo chown user:group file.txt
```

Change ownership recursively:

```bash
sudo chown -R user:group directory/
```

**Cybersecurity use case:** Correct ownership of application files and restricted directories.

**Warning:** Recursive ownership changes can break system services. Verify the target path and intended ownership first.

---

# Category 5: User and Privilege Management

## 21. `sudo` – Execute Commands with Elevated Privileges

Runs an authorized command with elevated privileges.

```bash
sudo apt update
```

Display permitted sudo commands:

```bash
sudo -l
```

**Cybersecurity use case:** Perform administrative operations while maintaining user accountability.

## 22. `su` – Switch User

Switches to another user account.

```bash
su username
```

Start a login shell:

```bash
su - username
```

**Cybersecurity use case:** Test account permissions and investigate user-specific environments.

## 23. `passwd` – Manage Passwords

Changes an account password.

```bash
passwd
```

Change another user's password as an administrator:

```bash
sudo passwd username
```

Lock password authentication:

```bash
sudo passwd -l username
```

Unlock password authentication:

```bash
sudo passwd -u username
```

**Cybersecurity use case:** Manage account credentials and respond to suspected password compromise.

**Note:** Locking a password does not necessarily revoke SSH keys, active sessions, or every other authentication method.

## 24. `useradd` – Create User Accounts

Creates a new user account.

```bash
sudo useradd -m analyst
```

Set a password:

```bash
sudo passwd analyst
```

**Cybersecurity use case:** Create dedicated accounts instead of sharing administrative credentials.

## 25. `usermod` – Modify User Accounts

Modify account settings and group membership.

Add a user to an existing group:

```bash
sudo usermod -aG sudo analyst
```

Lock an account:

```bash
sudo usermod -L analyst
```

**Cybersecurity use case:** Manage access privileges and remove unnecessary administrative membership.

**Important:** The `-aG` combination appends a supplementary group. Omitting `-a` can replace the user's existing supplementary group memberships.

---

# Category 6: Process and Service Management

## 26. `ps` – Display Running Processes

Displays information about running processes.

```bash
ps aux
```

Search for a particular process:

```bash
ps aux | grep nginx
```

Display process hierarchy:

```bash
ps -ef --forest
```

**Cybersecurity use case:** Investigate unusual processes and identify applications running on the system.

## 27. `top` – Monitor Processes in Real Time

Displays live process and resource information.

```bash
top
```

Useful information includes:

- CPU utilization.
- Memory utilization.
- Process IDs.
- Process ownership.
- System load.

**Cybersecurity use case:** Identify unexpected resource consumption that may require further investigation.

## 28. `kill` – Send Signals to Processes

Sends a signal to a process using its PID.

```bash
kill PID
```

Send a termination signal:

```bash
kill -TERM PID
```

Force termination:

```bash
kill -KILL PID
```

**Cybersecurity use case:** Stop a confirmed unwanted process during authorized administration or incident containment.

**Warning:** Investigate the process before terminating it. Killing a critical process can disrupt services or destroy volatile evidence.

## 29. `systemctl` – Manage System Services

Manages systemd services and other system units.

Check Nginx status:

```bash
systemctl status nginx
```

Start a service:

```bash
sudo systemctl start nginx
```

Stop a service:

```bash
sudo systemctl stop nginx
```

Enable a service at startup:

```bash
sudo systemctl enable nginx
```

**Cybersecurity use case:** Inspect exposed services and control unnecessary services.

## 30. `journalctl` – Read System Logs

Displays logs collected by systemd's journal.

Display recent logs:

```bash
sudo journalctl -n 50
```

Follow live logs:

```bash
sudo journalctl -f
```

Inspect SSH service logs:

```bash
sudo journalctl -u ssh
```

Show logs from the last hour:

```bash
sudo journalctl --since "1 hour ago"
```

**Cybersecurity use case:** Investigate authentication events, service failures, and suspicious system activity.

---

# Category 7: Networking Commands

## 31. `ip` – Network Configuration and Information

Displays and manages network interfaces, routes, and addresses.

Display network interfaces:

```bash
ip addr
```

Display routing information:

```bash
ip route
```

Display link-layer information:

```bash
ip link
```

**Cybersecurity use case:** Identify IP addresses, network interfaces, and routing paths during investigations.

## 32. `ping` – Test Network Reachability

Sends ICMP echo requests to a destination.

```bash
ping 8.8.8.8
```

Send a limited number of requests:

```bash
ping -c 4 8.8.8.8
```

**Cybersecurity use case:** Perform basic connectivity troubleshooting.

**Note:** A failed ping does not necessarily mean the destination is offline. ICMP may be blocked.

## 33. `ss` – Inspect Network Sockets

Displays socket information.

Show listening TCP and UDP sockets:

```bash
sudo ss -tulnp
```

Show established TCP connections:

```bash
ss -tn state established
```

Show listening TCP sockets:

```bash
sudo ss -tlnp
```

**Cybersecurity use case:** Identify exposed ports, listening services, and active network connections.

## 34. `curl` – Transfer Data Using URLs

Makes HTTP requests and supports several other protocols.

Retrieve a webpage:

```bash
curl https://example.com
```

Display HTTP response headers:

```bash
curl -I https://example.com
```

Display response headers and body:

```bash
curl -i https://example.com
```

Test a local web server:

```bash
curl -i http://127.0.0.1:8080/
```

**Cybersecurity use case:** Inspect HTTP responses, verify server behavior, and troubleshoot web applications.

## 35. `wget` – Download Files

Downloads content from supported URLs.

```bash
wget https://example.com/file.txt
```

Save with a different filename:

```bash
wget -O downloaded.txt https://example.com/file.txt
```

**Cybersecurity use case:** Retrieve authorized software packages, documentation, and lab resources.

**Security note:** Verify downloaded files using trusted checksums or signatures when available. Do not execute untrusted downloads.

---

# Category 8: Disk and Storage Commands

## 36. `df` – Check Filesystem Space

Displays available and used filesystem space.

```bash
df -h
```

Display filesystem types:

```bash
df -Th
```

**Cybersecurity use case:** Investigate unexpected disk usage and possible storage exhaustion caused by excessive logs or unwanted files.

## 37. `du` – Check Directory Usage

Displays disk usage for files and directories.

```bash
du -sh /var/log
```

Display sizes of immediate subdirectories:

```bash
du -h --max-depth=1 /var
```

**Cybersecurity use case:** Identify directories consuming unusual amounts of storage.

## 38. `lsblk` – List Block Devices

Displays storage devices and their partitions.

```bash
lsblk
```

Display filesystem information:

```bash
lsblk -f
```

**Cybersecurity use case:** Identify attached disks and mounted storage during system inventory.

## 39. `mount` – Inspect or Mount Filesystems

Display mounted filesystems:

```bash
mount
```

Display mounted filesystem information in a readable format:

```bash
findmnt
```

Mount an authorized filesystem:

```bash
sudo mount /dev/sdb1 /mnt
```

**Cybersecurity use case:** Inspect mounted storage and understand which devices are accessible.

**Warning:** Confirm device identity before mounting, formatting, or modifying storage.

## 40. `fdisk` – Inspect Disk Partitions

Displays and manages partition tables.

List disks and partitions:

```bash
sudo fdisk -l
```

**Cybersecurity use case:** Review disk layouts during system inventory and forensic preparation.

**Warning:** Partition modification can cause data loss. Use read-only inspection commands unless changes are explicitly required and backed up.

---

# Category 9: Security and Investigation Commands

## 41. `last` – Review Login History

Displays information about recent login sessions.

```bash
last
```

Display a limited number of records:

```bash
last -n 10
```

**Cybersecurity use case:** Investigate unusual login times, accounts, and session history.

## 42. `lastb` – Review Failed Login History

Displays failed login attempts where the system maintains the relevant records.

```bash
sudo lastb
```

**Cybersecurity use case:** Investigate repeated failed login attempts.

**Note:** Availability depends on distribution and logging configuration.

## 43. `lsof` – List Open Files

Displays files opened by processes.

List open files:

```bash
sudo lsof
```

Inspect files opened by a process:

```bash
sudo lsof -p PID
```

Inspect processes using a port:

```bash
sudo lsof -i :22
```

**Cybersecurity use case:** Identify which processes are using files, network ports, or sockets.

## 44. `sha256sum` – Calculate File Hashes

Calculates SHA-256 checksums.

```bash
sha256sum file.txt
```

Verify against a checksum file:

```bash
sha256sum -c checksums.txt
```

**Cybersecurity use case:** Verify file integrity and compare artifacts against trusted reference hashes.

**Important:** A matching hash confirms content equality with the trusted reference, not that the original file was safe.

## 45. `stat` – Display File Metadata

Displays file or filesystem metadata.

```bash
stat file.txt
```

Information may include:

- File size.
- Permissions.
- Ownership.
- Access time.
- Modification time.
- Metadata change time.

**Cybersecurity use case:** Investigate file metadata and support timeline analysis.

**Note:** File timestamps can be modified and should not be treated as conclusive evidence on their own.

---

# Category 10: Advanced Administration and Automation

## 46. `tar` – Archive Files

Creates and extracts archive files.

Create an archive:

```bash
tar -cvf backup.tar directory/
```

Create a compressed archive:

```bash
tar -czvf backup.tar.gz directory/
```

Extract an archive:

```bash
tar -xzvf backup.tar.gz
```

List archive contents without extracting:

```bash
tar -tzf backup.tar.gz
```

**Cybersecurity use case:** Package investigation files and create configuration backups.

## 47. `crontab` – Manage Scheduled Jobs

Manages scheduled tasks.

List current user's jobs:

```bash
crontab -l
```

Edit current user's jobs:

```bash
crontab -e
```

Example schedule:

```cron
0 2 * * * /home/analyst/backup.sh
```

This runs the script daily at 2:00 AM according to the system's configured time.

**Cybersecurity use case:** Review scheduled jobs for unauthorized persistence or suspicious automation.

## 48. `ssh` – Secure Remote Access

Connects to a remote Linux system over SSH.

```bash
ssh username@192.168.1.10
```

Connect using a custom port:

```bash
ssh -p 2222 username@192.168.1.10
```

Use a specified private key:

```bash
ssh -i ~/.ssh/id_ed25519 username@192.168.1.10
```

**Cybersecurity use case:** Secure remote administration and investigation.

## 49. `awk` – Process Structured Text

Processes and analyzes text, especially column-based data.

Display the first column:

```bash
awk '{print $1}' file.txt
```

Display the first and third columns:

```bash
awk '{print $1, $3}' file.txt
```

Count lines:

```bash
awk 'END {print NR}' file.txt
```

**Cybersecurity use case:** Extract selected fields from logs and prepare data for analysis.

## 50. `sort` – Sort Text Data

Sorts lines of text.

```bash
sort file.txt
```

Sort numerically:

```bash
sort -n numbers.txt
```

Sort and remove duplicate lines:

```bash
sort -u file.txt
```

**Cybersecurity use case:** Organize log values and prepare data for frequency analysis.

---

# 11. Combining Linux Commands for Security Investigations

Individual commands are useful, but combining them with pipes and redirection makes investigations more efficient.

## 11.1 Identify Repeated Failed SSH Logins

```bash
sudo grep "Failed password" /var/log/auth.log
```

Count matching events:

```bash
sudo grep "Failed password" /var/log/auth.log | wc -l
```

Extract source IPs from common OpenSSH failure messages:

```bash
sudo grep "Failed password" /var/log/auth.log \
| awk '{print $(NF-3)}' \
| sort \
| uniq -c \
| sort -nr
```

**Note:** Log formats vary. Validate the field extraction against the actual messages before relying on it.

## 11.2 Identify Listening Ports

```bash
sudo ss -tulnp
```

Investigate which process owns a particular port:

```bash
sudo lsof -i :22
```

## 11.3 Find Recently Modified Files

Find files modified in the last 24 hours under `/etc`:

```bash
sudo find /etc -type f -mtime -1 -print
```

## 11.4 Identify Large Log Directories

```bash
sudo du -h --max-depth=1 /var/log
```

## 11.5 Monitor Authentication Events in Real Time

```bash
sudo tail -f /var/log/auth.log
```

Alternatively, on a system using systemd:

```bash
sudo journalctl -f
```

## 11.6 Identify Unusual Running Processes

```bash
ps aux --sort=-%cpu | head
```

Inspect high-memory processes:

```bash
ps aux --sort=-%mem | head
```

High resource usage is an investigation clue, not proof of compromise.

---

# 12. Important Linux Operators and Symbols

Understanding shell operators is essential for combining commands.

| Operator | Purpose | Example |
|---|---|---|
| `\|` | Sends output to another command | `ps aux \| grep ssh` |
| `>` | Redirects output and overwrites a file | `ls > files.txt` |
| `>>` | Appends output to a file | `date >> activity.log` |
| `2>` | Redirects standard error | `find / -name test 2> errors.txt` |
| `&&` | Runs the next command if the previous succeeds | `sudo nginx -t && sudo systemctl reload nginx` |
| `;` | Runs commands sequentially | `pwd; whoami` |
| `*` | Wildcard matching | `ls *.log` |
| `~` | Current user's home directory | `cd ~` |
| `.` | Current directory | `./script.sh` |
| `..` | Parent directory | `cd ..` |

### Standard Input and Output

Linux commonly uses three standard streams:

- `stdin` (file descriptor 0): Standard input.
- `stdout` (file descriptor 1): Standard output.
- `stderr` (file descriptor 2): Standard error.

Example:

```bash
find /etc -name "*.conf" 2>/dev/null
```

This hides error messages by redirecting standard error to `/dev/null`.

During investigations, avoid discarding errors when those messages could provide important evidence.

---

# 13. Practical Cybersecurity Labs

Perform these labs on your own Linux VM or another authorized system.

## Lab 1: System Enumeration

**Objective:** Collect basic system information.

Commands:

```bash
uname -a
hostnamectl
whoami
id
uptime
ip addr
ip route
```

Tasks:

1. Identify the operating system and kernel.
2. Identify the current user.
3. Record group memberships.
4. Identify network interfaces and IP addresses.
5. Record system uptime.

**Expected result:** A basic system inventory.

## Lab 2: File Permission Investigation

**Objective:** Understand Linux file permissions.

Commands:

```bash
ls -l /etc/passwd
ls -l /etc/shadow
ls -ld ~/.ssh
```

Tasks:

1. Compare the permissions of the three locations.
2. Identify their owners.
3. Explain why `/etc/shadow` requires restricted access.
4. Explain why SSH private keys need restrictive permissions.

**Expected result:** An understanding of file ownership and least privilege.

## Lab 3: Network Investigation

**Objective:** Identify active network services.

Commands:

```bash
ip addr
ip route
sudo ss -tulnp
sudo ss -tn state established
```

Tasks:

1. Identify the system's IP addresses.
2. Identify listening ports.
3. Identify the processes associated with listening sockets.
4. Review established TCP connections.
5. Document services that require further investigation.

**Expected result:** A basic network exposure assessment.

## Lab 4: Authentication Log Analysis

**Objective:** Investigate login events.

Commands:

```bash
sudo grep -i "failed password" /var/log/auth.log
sudo grep -i "accepted" /var/log/auth.log
last
sudo lastb
```

Tasks:

1. Identify failed authentication events.
2. Identify successful SSH logins.
3. Record timestamps and account names.
4. Compare source addresses where available.
5. Identify unusual patterns for further investigation.

**Expected result:** A basic authentication investigation report.

## Lab 5: File Integrity Verification

**Objective:** Calculate and compare file hashes.

Create a test file:

```bash
echo "Linux Security Lab" > sample.txt
```

Calculate its hash:

```bash
sha256sum sample.txt
```

Modify the file:

```bash
echo "Modified content" >> sample.txt
```

Calculate the hash again:

```bash
sha256sum sample.txt
```

Tasks:

1. Record the original checksum.
2. Modify the file.
3. Calculate the new checksum.
4. Compare both values.
5. Explain how checksums help verify file integrity.

**Expected result:** An understanding of how content changes affect cryptographic hashes.

---

# 14. Best Practices for Using Linux Commands

1. Understand a command before running it with elevated privileges.
2. Use `sudo` only when required.
3. Verify file paths before deleting or modifying data.
4. Avoid blindly copying commands from unknown sources.
5. Use `man COMMAND` to read the manual.
6. Use `COMMAND --help` for available options.
7. Back up important configuration files before modifying them.
8. Preserve logs and evidence during investigations.
9. Verify command output rather than assuming success.
10. Avoid exposing passwords, private keys, or sensitive data in terminal output.
11. Test administrative changes in a lab environment first.
12. Document commands and findings during security investigations.

### Getting Help

Read the manual:

```bash
man ls
```

Display command help:

```bash
ls --help
```

Identify a command's executable path:

```bash
which ssh
```

For a more detailed command lookup:

```bash
type -a ssh
```

---

# 15. Interview Questions and Answers

### Q1. What is the purpose of the `uname` command?

It displays kernel and system information, including the kernel version and architecture.

### Q2. What is the difference between `whoami` and `id`?

`whoami` displays the effective username, while `id` displays user and group identity information.

### Q3. What is the difference between `grep` and `find`?

`grep` searches text patterns inside files or input streams. `find` searches filesystem entries based on conditions such as name, type, size, and modification time.

### Q4. What does `chmod 600` mean?

It gives the owner read and write permissions while denying permissions to the group and others.

### Q5. What is the purpose of `ps aux`?

It displays a detailed snapshot of running processes.

### Q6. How can you identify listening ports?

Use `ss -tulnp` to inspect listening TCP and UDP sockets and associated processes where permitted.

### Q7. What is the difference between `df` and `du`?

`df` displays filesystem space usage, while `du` displays the disk usage of files and directories.

### Q8. What is the purpose of `journalctl`?

It queries and displays logs collected by the systemd journal.

### Q9. What is the purpose of `sha256sum`?

It calculates or verifies SHA-256 checksums to help detect content changes.

### Q10. What does `crontab -l` do?

It lists scheduled cron jobs for the current user.

### Q11. What is the difference between `kill -TERM` and `kill -KILL`?

`TERM` requests graceful process termination. `KILL` forcibly terminates the process and cannot be caught or ignored.

### Q12. What does `ss -tulnp` show?

It displays listening TCP and UDP sockets, with numeric addresses and ports and process details where permissions allow.

### Q13. What is the purpose of `lsof`?

It lists open files, including files and network sockets associated with processes.

### Q14. Why is `sudo` important?

It provides controlled privilege elevation for administrative operations without requiring routine use of the root account.

### Q15. How would you investigate repeated failed SSH logins?

I would review authentication logs, identify timestamps and source addresses, correlate failed and successful logins, and investigate suspicious account activity.

---

# 16. Knowledge Check

Try answering these questions without looking at the command descriptions.

1. Which command displays the current user's identity?
2. Which command shows listening network ports?
3. Which command is used to search for text patterns?
4. What is the difference between `chmod` and `chown`?
5. Which command displays recent login history?
6. Which command follows a log file in real time?
7. What is the difference between `df` and `du`?
8. Which command displays systemd service logs?
9. Which command calculates a SHA-256 checksum?
10. How would you identify the process using TCP port 22?
11. Which command displays current network routes?
12. Why should you be careful with `rm -rf`?
13. What is the purpose of `awk` in log analysis?
14. Why is command output important during incident response?
15. How can you safely inspect a large log file?

---

# 17. Final Summary

Linux commands are foundational to cybersecurity operations. They help administrators and security analysts inspect systems, investigate processes, analyze logs, understand network exposure, manage access, and verify file integrity.

The most important skill is not simply memorizing commands. It is understanding:

- What a command does.
- Which privileges it requires.
- What its output means.
- What risks it may introduce.
- How to verify its result.
- How it contributes to a security investigation.

**Key takeaway:** A cybersecurity professional should be comfortable using Linux commands to observe system behavior, investigate suspicious activity, and perform authorized defensive operations.

---

## Recommended Next Learning Area

After completing these 50 commands, practice combining them into small investigation workflows.

Suggested progression:

1. Linux system enumeration.
2. User and permission auditing.
3. Network connection investigation.
4. Authentication log analysis.
5. Process investigation.
6. File integrity verification.
7. Linux incident-response exercises.

**Repository progress:** You have now covered Linux fundamentals, permissions, package management, Bash scripting, cron jobs, system logs, SSH, Nginx, hardening, and essential commands.
