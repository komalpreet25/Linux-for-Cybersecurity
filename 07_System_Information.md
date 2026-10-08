# Linux System Information

Linux provides many commands to inspect the operating system, kernel, CPU, memory, storage, hardware, network interfaces, and system uptime.

System information commands are useful for:

* Troubleshooting
* System administration
* Performance monitoring
* Security auditing
* Incident response
* Understanding the environment before performing authorized security testing

---

# 1. Why System Information Matters in Cybersecurity

Before troubleshooting or securing a Linux system, you should understand what is running on it.

For example, you may need to know:

* Which Linux distribution is installed?
* Which kernel version is being used?
* What CPU and RAM are available?
* How much disk space is available?
* Which storage devices exist?
* How long has the system been running?
* Which architecture is being used?
* Which hardware is connected?

This information helps you understand the system's configuration and identify potential security or compatibility issues.

---

# 2. Check the Current User

Use:

```bash
whoami
```

Example:

```text
komal
```

This tells you which user is currently executing commands.

You can also use:

```bash
id
```

This displays:

* UID
* GID
* Group memberships

Example:

```text
uid=1000(komal) gid=1000(komal) groups=1000(komal),27(sudo)
```

---

# 3. Check Hostname

The hostname identifies the system on a network.

Use:

```bash
hostname
```

Example:

```text
parrot
```

For more detailed hostname information:

```bash
hostnamectl
```

Example output may include:

```text
Static hostname: parrot
Operating System: Parrot GNU/Linux
Kernel: Linux 6.x.x
Architecture: x86-64
```

The exact output depends on the Linux distribution and configuration.

---

# 4. Check Linux Distribution

A common command is:

```bash
cat /etc/os-release
```

Example:

```text
NAME="Parrot Security"
VERSION="..."
ID=parrot
```

This file provides distribution identification information.

Another command:

```bash
lsb_release -a
```

This may provide:

* Distributor ID
* Description
* Release
* Codename

The command may not be installed on every distribution.

---

# 5. Check Kernel Version

The Linux kernel is the core component that manages communication between software and hardware.

Check the kernel version:

```bash
uname -r
```

Example:

```text
6.x.x-amd64
```

For more detailed information:

```bash
uname -a
```

This may display:

* Kernel name
* Hostname
* Kernel release
* Kernel version
* Machine architecture
* Operating system

---

# 6. Understanding `uname`

The `uname` command provides information about the system kernel.

### Kernel name

```bash
uname -s
```

### Kernel release

```bash
uname -r
```

### Kernel version

```bash
uname -v
```

### Machine architecture

```bash
uname -m
```

### All available information

```bash
uname -a
```

Common architecture output:

```text
x86_64
```

This indicates a 64-bit x86 architecture.

---

# 7. Check CPU Information

Linux provides detailed CPU information through:

```bash
cat /proc/cpuinfo
```

This can contain information such as:

* Processor model
* CPU cores
* CPU flags
* Vendor
* Clock-related information

A shorter overview can be obtained using:

```bash
lscpu
```

Example:

```bash
lscpu
```

Important information includes:

```text
Architecture
CPU op-mode(s)
CPU(s)
Thread(s) per core
Core(s) per socket
Model name
Virtualization
```

---

# 8. CPU Cores and Threads

Modern CPUs can have multiple cores and threads.

Use:

```bash
nproc
```

This displays the number of processing units available to the current environment.

Example:

```text
8
```

In a virtual machine, the value depends on the number of virtual CPUs assigned to the VM.

---

# 9. Check Memory Usage

Use:

```bash
free -h
```

Example:

```text
               total   used   free   shared   buff/cache   available
Mem:            15Gi    4Gi    6Gi      ...       ...          ...
Swap:            2Gi    0Gi    2Gi
```

The `-h` option means **human-readable**.

It converts values into easier units such as:

```text
MiB
GiB
```

Important memory concepts:

* **Total** → Total available memory.
* **Used** → Memory currently being used.
* **Free** → Completely unused memory.
* **Available** → Approximate memory available for new applications without heavy swapping.
* **Swap** → Disk-backed memory used when appropriate.

---

# 10. Check Memory Information

For detailed information:

```bash
cat /proc/meminfo
```

This contains many memory-related metrics.

For example:

```bash
grep -E 'MemTotal|MemFree|MemAvailable|SwapTotal|SwapFree' /proc/meminfo
```

This extracts selected memory values.

---

# 11. Check Disk Space

Use:

```bash
df -h
```

`df` means **disk filesystem** information.

Example:

```text
Filesystem      Size  Used Avail Use% Mounted on
/dev/sda1        50G   20G   28G  42% /
```

Important columns:

| Column     | Meaning               |
| ---------- | --------------------- |
| Filesystem | Storage filesystem    |
| Size       | Total filesystem size |
| Used       | Used space            |
| Avail      | Available space       |
| Use%       | Percentage used       |
| Mounted on | Mount point           |

---

# 12. Check Disk Usage of Directories

Use:

```bash
du -sh directory
```

Example:

```bash
du -sh /var/log
```

This shows the total size of the specified directory.

To see the sizes of items in the current directory:

```bash
du -sh *
```

This is useful when investigating why a filesystem is becoming full.

---

# 13. Check Block Devices

Use:

```bash
lsblk
```

This displays block devices such as:

* Hard drives
* SSDs
* Virtual disks
* Partitions
* Mount points

Example:

```text
NAME   SIZE TYPE MOUNTPOINT
sda     50G disk
├─sda1  48G part /
└─sda2   2G part [SWAP]
```

The actual device names and layout vary between systems.

---

# 14. Check Mounted Filesystems

Use:

```bash
findmnt
```

This displays mounted filesystems and their mount points.

You can also use:

```bash
mount
```

For example:

```bash
findmnt /
```

This helps determine which filesystem contains the root directory.

---

# 15. Check PCI Hardware

The `lspci` command displays PCI devices.

```bash
lspci
```

It may show:

* Network controllers
* Graphics controllers
* Audio devices
* USB controllers
* Storage controllers

For more detailed information:

```bash
lspci -nn
```

Depending on your permissions and installed tools, additional information may be available.

---

# 16. Check USB Devices

Use:

```bash
lsusb
```

This displays connected USB devices.

Example:

```text
Bus 001 Device 002: ID xxxx:xxxx ...
```

It can help identify:

* USB storage
* Keyboard
* Mouse
* Wireless adapters
* Other USB peripherals

---

# 17. Check Network Interfaces

Use:

```bash
ip link
```

or:

```bash
ip addr
```

Example interfaces may include:

```text
lo
eth0
enp0s3
wlan0
```

Common meanings:

* `lo` → Loopback interface
* `eth0` → Traditional Ethernet naming
* `enp0s3` → Predictable Ethernet interface name
* `wlan0` → Common wireless interface name

Actual interface names depend on the operating system and hardware.

---

# 18. Check IP Information

Use:

```bash
ip addr
```

or:

```bash
ip -br addr
```

The second command provides a shorter overview.

Example:

```text
lo       UNKNOWN   127.0.0.1/8
enp0s3   UP        192.168.1.10/24
```

This can help identify:

* Interface state
* IPv4 addresses
* IPv6 addresses
* Network prefixes

---

# 19. Check Routing Information

Use:

```bash
ip route
```

Example:

```text
default via 192.168.1.1 dev enp0s3
192.168.1.0/24 dev enp0s3 proto kernel scope link
```

This tells you how the system determines where network traffic should be sent.

---

# 20. Check DNS Configuration

On systems using traditional resolver configuration:

```bash
cat /etc/resolv.conf
```

You may see:

```text
nameserver 192.168.1.1
```

Modern Linux systems may use `systemd-resolved`, NetworkManager, or another resolver service, so `/etc/resolv.conf` may be a symbolic link or generated file.

A useful command on systems using `systemd-resolved` is:

```bash
resolvectl status
```

---

# 21. Check System Uptime

Use:

```bash
uptime
```

Example:

```text
18:30:10 up 2 days, 4:21, 2 users, load average: 0.20, 0.15, 0.10
```

This tells you:

* Current time
* System uptime
* Number of logged-in users
* Load averages

You can also use:

```bash
uptime -p
```

Example:

```text
up 2 days, 4 hours
```

---

# 22. Check System Boot Time

Use:

```bash
uptime -s
```

Example:

```text
2026-10-06 14:10:23
```

This can help during troubleshooting and incident investigation.

---

# 23. Check Current Date and Time

Use:

```bash
date
```

Example:

```text
Thu Oct 8 13:20:00 IST 2026
```

Correct system time is important for:

* Logs
* Authentication
* Certificates
* Scheduled jobs
* Incident investigation

---

# 24. Check Kernel Messages

The `dmesg` command displays messages from the kernel's ring buffer.

```bash
dmesg
```

For easier reading:

```bash
dmesg | less
```

To view recent messages:

```bash
dmesg | tail
```

Depending on system configuration, access to some kernel messages may require elevated privileges.

Security and troubleshooting uses include investigating:

* Hardware problems
* Driver issues
* Boot problems
* Device detection
* Kernel-related errors

---

# 25. Check System Architecture

Use:

```bash
uname -m
```

Example:

```text
x86_64
```

Another command:

```bash
arch
```

This can also display the machine architecture.

Common architectures include:

```text
x86_64
aarch64
armv7l
```

---

# 26. Check Virtualization

When working with VirtualBox, VMware, cloud systems, or other virtual environments, it can be useful to identify whether the system is virtualized.

Try:

```bash
systemd-detect-virt
```

Possible output could include:

```text
oracle
```

for a VirtualBox environment.

You can also check:

```bash
lscpu
```

for virtualization-related information.

---

# 27. Check Kernel and OS Information Together

A useful combination is:

```bash
uname -a
cat /etc/os-release
```

For a broader overview:

```bash
hostnamectl
```

These commands can quickly establish:

```text
Hostname
OS
Kernel
Architecture
```

---

# 28. System Information for Cybersecurity

System information is useful during security assessments and incident response.

For example, an authorized security analyst may collect:

```bash
uname -a
cat /etc/os-release
hostnamectl
id
ip addr
ip route
lsblk
df -h
free -h
```

This helps establish the system's:

* Operating system
* Kernel
* User context
* Network configuration
* Storage configuration
* Memory availability
* Hardware environment

**Important:** Collect system information only on systems you own or are authorized to assess.

---

# 29. Security-Relevant Information

Some system information can help identify security risks.

### Old Kernel

An outdated kernel may lack security fixes.

Check:

```bash
uname -r
```

Then compare the installed version against the security support information for your distribution.

### Low Disk Space

A nearly full filesystem can cause services to fail.

Check:

```bash
df -h
```

### Unexpected Hardware

Review:

```bash
lsusb
lspci
```

to understand connected hardware.

### Unexpected Network Interfaces

Check:

```bash
ip link
```

Unexpected interfaces should be investigated according to the system's expected configuration.

---

# 30. Practical System Information Lab

Run the following commands on your Linux VM.

### Step 1 — Current User

```bash
whoami
```

### Step 2 — User and Group Information

```bash
id
```

### Step 3 — Hostname

```bash
hostname
```

### Step 4 — OS Information

```bash
cat /etc/os-release
```

### Step 5 — Kernel

```bash
uname -r
```

### Step 6 — CPU

```bash
lscpu
```

### Step 7 — Memory

```bash
free -h
```

### Step 8 — Storage

```bash
lsblk
df -h
```

### Step 9 — Network

```bash
ip -br addr
ip route
```

### Step 10 — Uptime

```bash
uptime
```

After running these commands, you should be able to describe the basic configuration of your Linux VM.

---

# 31. Quick System Information Checklist

When you need a quick overview of a Linux system:

```bash
whoami
hostnamectl
cat /etc/os-release
uname -r
lscpu
free -h
lsblk
df -h
ip -br addr
ip route
uptime
```

---

# 32. Important Interview Questions

### Q1. How do you check the Linux distribution?

```bash
cat /etc/os-release
```

### Q2. How do you check the kernel version?

```bash
uname -r
```

### Q3. What does `uname -a` show?

It displays several system and kernel details, including the kernel name, hostname, kernel release, kernel version, and machine architecture.

### Q4. How do you check CPU information?

```bash
lscpu
```

### Q5. How do you check memory usage?

```bash
free -h
```

### Q6. How do you check disk space?

```bash
df -h
```

### Q7. What is the difference between `df` and `du`?

`df` shows available and used space on filesystems, while `du` estimates the space used by files and directories.

### Q8. How do you view disks and partitions?

```bash
lsblk
```

### Q9. How do you check network interfaces?

```bash
ip addr
```

or:

```bash
ip link
```

### Q10. How do you check the routing table?

```bash
ip route
```

### Q11. How do you check system uptime?

```bash
uptime
```

### Q12. How do you check connected USB devices?

```bash
lsusb
```

### Q13. How do you check PCI devices?

```bash
lspci
```

### Q14. Why is system information important in cybersecurity?

It helps security professionals understand the operating system, kernel, hardware, network configuration, users, and system state so they can troubleshoot, audit, harden, and investigate the system effectively.

---

# 33. Quick Revision

```text
whoami             → Current user
id                 → UID, GID, groups
hostname           → Hostname
hostnamectl        → System/hostname information
cat /etc/os-release → Linux distribution
uname -r           → Kernel version
uname -a           → Detailed kernel/system information
lscpu              → CPU information
nproc              → Available processing units
free -h            → Memory usage
cat /proc/meminfo  → Detailed memory information
df -h              → Filesystem disk usage
du -sh             → Directory size
lsblk              → Block devices
findmnt            → Mounted filesystems
lspci              → PCI hardware
lsusb              → USB devices
ip addr            → IP/interface information
ip route            → Routing table
uptime             → Uptime/load
date               → System date/time
dmesg              → Kernel messages
```

---

# 34. Summary

Linux provides many built-in tools for understanding the system's configuration and current state.

The most important commands to remember are:

```bash
uname -a
cat /etc/os-release
hostnamectl
lscpu
free -h
df -h
du -sh
lsblk
ip addr
ip route
uptime
```

For cybersecurity, these commands help with:

* System identification
* Troubleshooting
* Security auditing
* Incident response
* Performance analysis
* System hardening

**Key takeaway:** Before securing or troubleshooting a Linux system, first understand what operating system, kernel, hardware, storage, network configuration, and user context you are working with.
