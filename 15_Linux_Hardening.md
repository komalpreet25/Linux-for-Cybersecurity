
# 15. Linux Hardening for Cybersecurity

## 1. Introduction to Linux Hardening

**Linux hardening** is the process of reducing a Linux system's attack surface by securely configuring the operating system, restricting unnecessary access, minimizing exposed services, applying updates, and monitoring security events.

A default installation may include services, accounts, permissions, or configurations that are unnecessary for a particular system. Hardening aligns these settings with the system's purpose and security requirements.

### Objectives of Linux Hardening

- Reduce the attack surface.
- Prevent unauthorized access.
- Apply the principle of least privilege.
- Protect sensitive files and credentials.
- Secure network-facing services.
- Improve logging, auditing, and detection.
- Reduce the impact of compromised accounts.
- Support incident investigation and recovery.

### Core Security Principles

| Principle | Explanation |
|---|---|
| Least privilege | Give users and processes only the permissions they need. |
| Defense in depth | Use multiple complementary security controls. |
| Minimize attack surface | Disable unnecessary services, software, and access paths. |
| Secure configuration | Apply appropriate settings consistently. |
| Continuous monitoring | Detect suspicious activity and configuration changes. |
| Patch management | Fix known vulnerabilities through tested updates. |
| Recovery readiness | Maintain tested backups and incident-response procedures. |

**Important:** Hardening is not a one-time task. It requires regular assessment, maintenance, and verification.

---

## 2. Assess the System Before Hardening

Before changing security settings, understand the system's role and current state.

### Identify the Operating System

```bash
cat /etc/os-release
```

Display the kernel version:

```bash
uname -r
```

Display system architecture:

```bash
uname -m
```

Display system information:

```bash
hostnamectl
```

### Identify the Current User

```bash
whoami
id
groups
```

Check users with administrative privileges:

```bash
getent group sudo
```

On some distributions, administrative access may use the `wheel` group instead:

```bash
getent group wheel
```

### Check Listening Network Ports

```bash
sudo ss -tulpen
```

This helps identify listening TCP/UDP sockets, associated processes, and relevant details where available.

### Review Running Services

```bash
systemctl --type=service --state=running
```

List enabled services:

```bash
systemctl list-unit-files --type=service --state=enabled
```

### Check Disk Space

```bash
df -h
```

Check memory usage:

```bash
free -h
```

### Security Assessment Questions

Before making changes, ask:

1. What is this system used for?
2. Which services must remain available?
3. Which users need administrative access?
4. Which ports must be reachable?
5. Are backups available and restorable?
6. Could a change lock out remote administrators?
7. How will the effectiveness of each change be verified?

Document the baseline so that changes can be compared against the original state.

---

## 3. Patch Management and Software Security

Unpatched software may contain known vulnerabilities that attackers can exploit.

### Debian-Based Systems

Refresh package information:

```bash
sudo apt update
```

Review available upgrades:

```bash
apt list --upgradable
```

Upgrade installed packages:

```bash
sudo apt upgrade
```

Remove unused dependencies:

```bash
sudo apt autoremove
```

Review package details:

```bash
apt show openssh-server
```

Check whether a package is installed:

```bash
dpkg -l openssh-server
```

### Security Considerations

- Apply security updates promptly according to risk.
- Review important changes before deploying them to production.
- Test updates on suitable systems before broad rollout.
- Plan for services that require restarts.
- Monitor vendor security advisories.
- Maintain a rollback or recovery plan.
- Avoid leaving systems unpatched indefinitely because of maintenance inconvenience.

### Check Update History

On Debian-based systems, inspect package history where available:

```bash
sudo less /var/log/apt/history.log
```

Package and update logging varies by distribution.

**Best practice:** Patch management should include an inventory of software, prioritization by risk, controlled deployment, and verification that updates were successfully applied.

---

## 4. User and Account Hardening

Compromised accounts can provide attackers with an initial foothold. Strong account management reduces this risk.

### Review Local Accounts

Display account names:

```bash
cut -d: -f1 /etc/passwd
```

Display accounts with a login shell:

```bash
awk -F: '$7 !~ /(nologin|false)$/ {print $1, $7}' /etc/passwd
```

This is a review aid, not a definitive list of human users. Some service accounts may have login shells, and legitimate users may use restricted shells.

### Review Administrative Access

```bash
getent group sudo
```

Check whether your account can use sudo:

```bash
sudo -l
```

Review administrative permissions and remove unnecessary privileges through an approved process.

### Create a Dedicated Administrative User

On a Debian-based lab machine:

```bash
sudo adduser adminuser
sudo usermod -aG sudo adminuser
```

Replace `adminuser` with an appropriate username.

Verify membership:

```bash
id adminuser
```

Only grant administrative privileges when required. Do not add ordinary users to the sudo group without a legitimate need.

### Review Account Password Aging

Inspect a user's password-aging settings:

```bash
sudo chage -l username
```

Set an account expiration date only when appropriate:

```bash
sudo chage -E YYYY-MM-DD username
```

Replace the placeholder with the intended date. Account expiration should follow organizational policy and must not unexpectedly disable critical service accounts.

### Disable an Unused Account

When an account is no longer needed, first determine whether it owns services, scheduled jobs, or important files. Then disable access using the organization's approved account-management process.

For example, locking password authentication:

```bash
sudo passwd -l username
```

**Important:** Locking a password does not necessarily disable every authentication method or terminate existing sessions. SSH keys, active processes, and other credentials may need separate review.

---

## 5. Password and Authentication Security

Weak authentication can undermine otherwise secure systems.

### Review Password Policy

Check the authentication configuration and the tools used by the distribution. On many Linux systems, PAM and related configuration files influence authentication behavior.

Common locations include:

```text
/etc/pam.d/
/etc/login.defs
/etc/security/
```

Do not edit PAM configuration without understanding the consequences. A mistake may prevent legitimate users from logging in.

### General Authentication Recommendations

- Use unique, strong passwords where passwords are permitted.
- Avoid shared accounts.
- Use multi-factor authentication where supported and appropriate.
- Prefer SSH public-key authentication for suitable administrative workflows.
- Protect private keys with restrictive permissions and a passphrase.
- Remove obsolete authentication credentials.
- Apply login throttling or lockout controls carefully.
- Monitor failed and successful authentication events.

### Password Hashing

Linux systems generally store password hashes rather than plaintext passwords in the protected `/etc/shadow` file.

Inspect permissions:

```bash
ls -l /etc/shadow
```

Do not publish, copy, or expose password hashes. A stolen password hash can be attacked offline.

---

## 6. File Permissions and Ownership

Linux permissions control access to files and directories.

### Understand Permission Bits

Example:

```text
-rwxr-x---
```

The permission groups represent:

- Owner: `rwx`
- Group: `r-x`
- Others: `---`

For regular files:
- `r` means read.
- `w` means write.
- `x` means execute.

For directories, the meanings differ slightly: read lists entries, write changes directory entries, and execute permits traversal subject to other permissions.

### Inspect Permissions

```bash
ls -l /etc/passwd
ls -l /etc/shadow
ls -ld /etc/ssh
```

Inspect parent-directory permissions:

```bash
namei -l /path/to/file
```

### Change Permissions

Restrict a private file:

```bash
chmod 600 private.txt
```

Allow the owner to access a directory:

```bash
chmod 700 secure-directory
```

Set common SSH private-key permissions:

```bash
chmod 600 ~/.ssh/id_ed25519
```

### Change Ownership

```bash
sudo chown user:group file.txt
```

Recursively change ownership only when you fully understand which files will be affected:

```bash
sudo chown -R user:group /path/to/directory
```

Avoid using recursive permission or ownership commands on broad system directories.

### Find World-Writable Files

On a system you administer:

```bash
sudo find / -xdev -type f -perm -0002 -print 2>/dev/null
```

This searches the current filesystem for regular files with the world-write permission bit set. Some results may be legitimate, and mounted filesystems are excluded by `-xdev`.

### Find World-Writable Directories

```bash
sudo find / -xdev -type d -perm -0002 -print 2>/dev/null
```

Review sticky-bit behavior and directory purpose before changing permissions.

**Security principle:** Do not blindly change every writable file. Determine why the permission exists, identify the owner and application, and apply the least disruptive correction.

---

## 7. SUID, SGID, and Special Permissions

Special permission bits can allow programs to operate with specific ownership or group behavior.

### SUID

The Set User ID bit can cause an executable to run with the file owner's effective user ID.

Find SUID files on the current filesystem:

```bash
sudo find / -xdev -type f -perm -4000 -print 2>/dev/null
```

### SGID

The Set Group ID bit can affect an executable's effective group ID. On directories, it commonly causes new files to inherit the directory's group.

Find SGID files:

```bash
sudo find / -xdev -type f -perm -2000 -print 2>/dev/null
```

### Review Results Carefully

A SUID or SGID file is not automatically vulnerable. Many are legitimate system utilities.

Investigate:
- Whether the file is expected.
- Whether its package is trusted and up to date.
- Whether its permissions or ownership have changed.
- Whether the executable has a documented security purpose.
- Whether unnecessary special privileges can be safely removed.

Do not remove special permissions from system binaries without confirming the operational impact.

---

## 8. SSH Hardening

SSH is commonly used for remote administration and should be configured according to the system's role.

### Review the SSH Server Configuration

```bash
sudo sshd -t
```

Display effective settings:

```bash
sudo sshd -T
```

Main configuration file:

```text
/etc/ssh/sshd_config
```

### Example Hardening Settings

```text
PermitRootLogin no
PermitEmptyPasswords no
PubkeyAuthentication yes
```

These are examples, not a complete universal configuration. Other authentication settings must be chosen based on your access model.

If public-key access has been configured and verified, administrators may choose to disable password authentication:

```text
PasswordAuthentication no
```

### Apply Changes Safely

1. Back up the configuration.
2. Confirm that another authorized administrative access method is available.
3. Edit the configuration.
4. Validate syntax using `sshd -t`.
5. Reload the service.
6. Test a new login before closing the original session.

Example:

```bash
sudo cp /etc/ssh/sshd_config /etc/ssh/sshd_config.backup
sudo sshd -t
sudo systemctl reload ssh
```

Some systems use `sshd` rather than `ssh` as the service name. Verify the correct service for your distribution.

### Additional Recommendations

- Restrict access to trusted networks where possible.
- Remove obsolete public keys from `authorized_keys`.
- Monitor authentication logs.
- Keep OpenSSH updated.
- Verify server host keys.
- Avoid unnecessary port forwarding.
- Do not expose administrative access more broadly than required.

---

## 9. Firewall Configuration

A host firewall helps control inbound and outbound network traffic.

The appropriate firewall depends on the distribution, network design, and existing management tools.

### UFW on Debian-Based Systems

Check status:

```bash
sudo ufw status verbose
```

Allow SSH before enabling UFW on a remotely managed machine:

```bash
sudo ufw allow OpenSSH
```

Allow HTTP:

```bash
sudo ufw allow 80/tcp
```

Allow HTTPS:

```bash
sudo ufw allow 443/tcp
```

Enable the firewall:

```bash
sudo ufw enable
```

Review the rules:

```bash
sudo ufw status numbered
```

Remove a rule by its displayed number:

```bash
sudo ufw delete NUMBER
```

Replace `NUMBER` with the actual rule number shown by UFW.

### Important Precautions

- Verify the SSH rule before enabling the firewall remotely.
- Confirm which management ports must remain available.
- Avoid adding broad allow rules unnecessarily.
- Review existing firewall tooling before changing it.
- Test both allowed and denied traffic from an authorized client.
- Remember that a host firewall complements, rather than replaces, network-level controls.

**Warning:** Firewall changes can interrupt connectivity. Keep console access or a tested recovery method available.

---

## 10. Disable Unnecessary Services

Every unnecessary network-facing service can increase the attack surface.

### List Running Services

```bash
systemctl --type=service --state=running
```

### List Listening Sockets

```bash
sudo ss -tulpen
```

### Inspect a Service

```bash
systemctl status SERVICE_NAME
```

Replace `SERVICE_NAME` with the service you are investigating.

### Stop an Unneeded Service

```bash
sudo systemctl stop SERVICE_NAME
```

Disable it at boot if it is not required:

```bash
sudo systemctl disable SERVICE_NAME
```

Only disable services after confirming their purpose and dependencies.

For example, a database, remote-management agent, or monitoring service may be essential even if it is not directly used by an interactive user.

### Security Review

For each service, document:
- Purpose.
- Owner or responsible team.
- Listening address and port.
- Required users or applications.
- Patch status.
- Whether it must be reachable from other systems.

The objective is not to stop as many services as possible. It is to retain necessary services and secure them appropriately.

---

## 11. Kernel and Network Hardening

Linux provides kernel parameters that can influence network behavior and security.

Some parameters are configured through `sysctl`.

### Inspect Current Parameters

```bash
sysctl net.ipv4.ip_forward
```

Inspect reverse-path filtering:

```bash
sysctl net.ipv4.conf.all.rp_filter
```

List available IPv4 forwarding-related settings:

```bash
sysctl -a 2>/dev/null | grep '^net.ipv4'
```

### IP Forwarding

IP forwarding is necessary for some routers, gateways, VPNs, and container or virtualization setups.

Check the current value:

```bash
sysctl net.ipv4.ip_forward
```

A value of `1` enables IPv4 forwarding; `0` disables it.

Disable forwarding only if the system does not require routing:

```bash
sudo sysctl -w net.ipv4.ip_forward=0
```

This runtime change is not necessarily persistent across reboot.

### Persistent Settings

Persistent `sysctl` configuration is commonly stored in:

```text
/etc/sysctl.conf
/etc/sysctl.d/
```

Example configuration for a system that should not route IPv4 traffic:

```text
net.ipv4.ip_forward = 0
```

Apply settings from a specific file:

```bash
sudo sysctl -p /etc/sysctl.conf
```

Review the resulting values afterward.

### Security Considerations

- Do not disable forwarding on a system acting as a router or VPN gateway.
- Do not blindly apply internet-sourced kernel hardening snippets.
- Understand how settings interact with containers, VPNs, and virtual machines.
- Test changes before deploying them to production.
- Document the reason for every non-default security parameter.

---

## 12. Logging and Auditing

Logs provide evidence for investigating authentication issues, service failures, and suspicious activity.

### Authentication Logs

On many Debian-based systems:

```bash
sudo tail -f /var/log/auth.log
```

On systemd-based systems:

```bash
sudo journalctl --since "1 hour ago"
```

Inspect SSH events:

```bash
sudo journalctl -u ssh --since "1 hour ago"
```

The service name and log destination may differ by distribution.

### Login History

Show recent login records:

```bash
last
```

Show failed login records where supported:

```bash
sudo lastb
```

The availability of failed-login history depends on the system and its logging configuration.

### Kernel Messages

```bash
sudo dmesg --level=err,warn
```

### Auditd

The Linux Audit framework can record selected security-relevant events.

Install on Debian-based systems:

```bash
sudo apt install auditd audispd-plugins
```

Check status:

```bash
sudo systemctl status auditd
```

Search audit records where auditd is configured and running:

```bash
sudo ausearch -m USER_LOGIN
```

Audit rules determine which events are collected. Installing auditd alone does not guarantee that every relevant action will be recorded.

### Logging Best Practices

- Restrict access to logs.
- Configure appropriate retention and rotation.
- Monitor important authentication and privilege events.
- Protect logs from unauthorized modification.
- Synchronize system time.
- Forward important events to a centralized logging system where appropriate.
- Test that the events you expect are actually being collected.

---

## 13. File Integrity Monitoring

File integrity monitoring helps identify unexpected changes to important system files.

### Generate a Checksum

```bash
sha256sum /etc/ssh/sshd_config
```

Record the checksum through an approved process.

Later, calculate it again:

```bash
sha256sum /etc/ssh/sshd_config
```

A changed checksum indicates that the file content differs, but it does not explain why it changed.

### Important Limitations

- A checksum does not establish whether the original file was trustworthy.
- The baseline must be protected against unauthorized modification.
- Legitimate package updates and configuration changes also change checksums.
- A compromised host may be able to alter local monitoring data.

For broader monitoring, consider a properly configured file integrity monitoring tool such as AIDE or an enterprise endpoint monitoring solution.

---

## 14. Protect Scheduled Tasks and Startup Mechanisms

Attackers may abuse scheduled jobs or startup mechanisms to maintain access. Administrators should review them as part of routine hardening.

### Review Current User's Cron Jobs

```bash
crontab -l
```

Review root's cron jobs:

```bash
sudo crontab -l
```

Inspect common scheduled-task directories:

```bash
ls -la /etc/cron.d/
ls -la /etc/cron.daily/
ls -la /etc/cron.hourly/
```

### Review Enabled Services

```bash
systemctl list-unit-files --type=service --state=enabled
```

### Investigation Questions

- Is the task expected?
- Who owns it?
- Does the command point to a trusted executable?
- Has the task recently changed?
- Are scripts writable by untrusted users?
- Does the task run with unnecessary privileges?

Do not delete scheduled jobs simply because their purpose is unfamiliar. Verify their ownership and function first.

---

## 15. Backup and Recovery Security

Backups are essential for recovering from accidental changes, hardware failures, and security incidents.

### Backup Principles

- Follow the 3-2-1 backup principle where practical: maintain multiple copies, use different storage types, and keep an off-site copy.
- Restrict access to backup systems.
- Encrypt backups containing sensitive information.
- Keep credentials for backup infrastructure separate from the systems being protected.
- Maintain offline or immutable recovery copies where feasible.
- Test restoration regularly.
- Monitor backup failures and unexpected changes.

### Example: Back Up an Important Configuration

```bash
sudo cp /etc/ssh/sshd_config \
/etc/ssh/sshd_config.backup
```

This creates a local copy, but it is not a substitute for a properly managed backup strategy.

### Recovery Verification

A backup is useful only if it can be restored.

Document:
- What is backed up.
- How frequently it is backed up.
- Where copies are stored.
- Who can access them.
- How restoration is performed.
- How recovery is verified.

---

## 16. Security Assessment Tools

Several tools can support Linux security assessment. Their output must be interpreted in context.

### Lynis

Lynis performs security auditing and provides recommendations.

Install on a supported Debian-based system when available:

```bash
sudo apt update
sudo apt install lynis
```

Run a system audit:

```bash
sudo lynis audit system
```

Review the report and recommendations. Not every warning represents a vulnerability, and not every recommendation is appropriate for every system.

### OpenSCAP

OpenSCAP can evaluate systems against supported security policies and compliance content.

Its availability and setup depend on the distribution, installed packages, and selected policy.

### Package and Service Checks

```bash
sudo apt list --upgradable
sudo ss -tulpen
systemctl --type=service --state=running
```

These checks provide useful baseline information even without a dedicated auditing tool.

### Assessment Workflow

1. Define the system's role and scope.
2. Collect baseline information.
3. Run an appropriate audit.
4. Validate each finding.
5. Prioritize by severity and exposure.
6. Plan corrective actions.
7. Apply changes in a controlled manner.
8. Retest and document the results.

---

## 17. Practical Linux Hardening Labs

Perform these labs on a local virtual machine or a system you are authorized to administer. Take a VM snapshot or backup before making significant changes.

### Lab 1: Establish a Security Baseline

**Objective:** Identify the system's current security exposure.

Run:

```bash
cat /etc/os-release
uname -r
whoami
id
sudo ss -tulpen
systemctl --type=service --state=running
df -h
```

Tasks:
1. Record the operating system and kernel.
2. Identify listening ports.
3. List running services.
4. Identify unnecessary services for the VM's role.
5. Document the results before changing anything.

**Expected result:** A baseline inventory of the system.

### Lab 2: Review File Permissions

**Objective:** Identify potentially risky file permissions.

Run:

```bash
ls -l /etc/passwd
ls -l /etc/shadow
ls -ld /etc/ssh
sudo find / -xdev -type f -perm -0002 -print 2>/dev/null
```

Tasks:
1. Compare permissions on sensitive files.
2. Understand owner, group, and other permissions.
3. Investigate world-writable files.
4. Determine whether each permission is required.
5. Document any approved corrections.

**Expected result:** You can explain why file permissions matter and avoid unsafe blanket changes.

### Lab 3: Harden SSH

**Objective:** Improve remote-access security.

Tasks:
1. Back up the SSH configuration.
2. Review root-login settings.
3. Verify public-key authentication.
4. Validate configuration syntax.
5. Reload the service safely.
6. Test a new connection.
7. Review authentication logs.

Commands:

```bash
sudo cp /etc/ssh/sshd_config /etc/ssh/sshd_config.backup
sudo sshd -t
sudo sshd -T
sudo journalctl -u ssh --since "30 minutes ago"
```

**Expected result:** You can inspect SSH security settings and verify that legitimate access continues to work.

### Lab 4: Configure a Host Firewall

**Objective:** Allow only required inbound access.

Tasks:
1. Inspect existing firewall rules.
2. Determine which ports are required.
3. Allow the correct SSH access before enabling the firewall.
4. Enable UFW on the test VM.
5. Verify the resulting rules.
6. Test connectivity from an authorized client.

Commands:

```bash
sudo ufw status verbose
sudo ufw allow OpenSSH
sudo ufw enable
sudo ufw status numbered
```

**Expected result:** You can explain which inbound connections are allowed and why.

### Lab 5: Audit Running Services

**Objective:** Reduce unnecessary attack surface.

Tasks:
1. List running services.
2. Identify services that listen on network interfaces.
3. Research the purpose of unfamiliar services.
4. Determine whether each is required.
5. Stop and disable only a confirmed unnecessary service in your lab.
6. Verify that the system still functions.

Commands:

```bash
systemctl --type=service --state=running
sudo ss -tulpen
systemctl status SERVICE_NAME
```

Replace `SERVICE_NAME` with a service you have identified and verified.

**Expected result:** A documented service review with justified actions.

### Lab 6: Perform a Basic Security Audit

**Objective:** Use an auditing tool to identify improvement opportunities.

If Lynis is available:

```bash
sudo apt update
sudo apt install lynis
sudo lynis audit system
```

Tasks:
1. Review the audit summary.
2. Select three findings.
3. Verify each finding manually.
4. Prioritize the findings by risk.
5. Implement an appropriate correction in your lab.
6. Run the audit again and compare the results.

**Expected result:** You can distinguish an audit recommendation from a verified security issue.

---

## 18. Incident Response: Suspected Linux Compromise

If you suspect a system has been compromised, avoid making uncontrolled changes that could destroy evidence.

### Initial Investigation

1. Follow your organization's incident-response procedures.
2. Record the time, affected host, observed symptoms, and actions taken.
3. Preserve relevant system, authentication, and application logs.
4. Identify unusual processes, network connections, and account changes.
5. Review recent modifications to SSH configuration, scheduled jobs, and startup services.
6. Determine whether credentials or keys may have been exposed.
7. Assess whether containment is required.
8. Preserve forensic evidence according to the incident's requirements.

### Useful Commands

Inspect processes:

```bash
ps aux
```

Inspect network connections and listening sockets:

```bash
sudo ss -tupna
```

Inspect recent logins:

```bash
last
```

Review recent system journal entries:

```bash
sudo journalctl --since "1 hour ago"
```

Inspect recently modified files in a specific directory:

```bash
sudo find /etc -type f -mmin -60 -print
```

The last command lists files modified in the previous hour; it does not prove that those changes are malicious.

### After Confirmation

Depending on the incident:
- Contain the affected system.
- Revoke exposed credentials and keys.
- Remove unauthorized access using a controlled process.
- Patch the root cause.
- Restore from a trusted source when appropriate.
- Verify system integrity.
- Monitor for recurrence.
- Document lessons learned.

Do not assume that deleting one suspicious file fully removes an attacker from a compromised system.

---

## 19. Common Hardening Mistakes

### Mistake 1: Applying Commands Without Understanding Them

A command that is appropriate for a desktop may break a server, router, or VPN gateway.

**Better approach:** Understand the system's role, inspect the impact, and test changes first.

### Mistake 2: Disabling Every Unfamiliar Service

Some services support networking, security monitoring, backups, or system stability.

**Better approach:** Verify purpose and dependencies before disabling anything.

### Mistake 3: Setting Permissions to `777`

This grants broad access and can expose sensitive files to modification.

**Better approach:** Apply the minimum required permissions and ownership.

### Mistake 4: Disabling Password Authentication Too Early

Incorrect SSH key configuration can lock out remote administrators.

**Better approach:** Test key-based access in a separate session before changing authentication policy.

### Mistake 5: Treating a Firewall as Complete Protection

A firewall cannot fix vulnerable software, unsafe permissions, or compromised credentials by itself.

**Better approach:** Combine network restrictions with patching, access control, monitoring, and recovery planning.

### Mistake 6: Trusting Every Scanner Finding

Automated tools can produce false positives or recommendations unsuitable for a specific environment.

**Better approach:** Validate findings and assess the actual risk before making changes.

### Mistake 7: Ignoring Recovery

Aggressive hardening can break legitimate access or application functionality.

**Better approach:** Maintain backups, test recovery, and document a rollback plan.

---

## 20. Linux Hardening Checklist

Use this checklist during a controlled security review.

### System and Software

- [ ] Identify the operating system and its purpose.
- [ ] Review available security updates.
- [ ] Remove unnecessary software where appropriate.
- [ ] Record the system's baseline configuration.

### Accounts and Access

- [ ] Review local accounts and administrative groups.
- [ ] Remove unnecessary privileges.
- [ ] Secure authentication methods.
- [ ] Review SSH configuration and authorized keys.
- [ ] Protect sensitive files and credentials.

### Network and Services

- [ ] Identify listening ports.
- [ ] Review running and enabled services.
- [ ] Restrict network access to required services.
- [ ] Verify firewall rules.
- [ ] Review IP forwarding and other relevant network settings.

### Monitoring and Recovery

- [ ] Confirm authentication and service logs are available.
- [ ] Protect logs and configure appropriate retention.
- [ ] Review scheduled tasks and startup mechanisms.
- [ ] Consider file integrity monitoring.
- [ ] Verify backup access controls.
- [ ] Test restoration procedures.

### Verification

- [ ] Validate configuration syntax.
- [ ] Test essential application functionality.
- [ ] Confirm legitimate administrative access.
- [ ] Retest important security controls.
- [ ] Document changes, findings, and exceptions.

---

## 21. Interview Questions and Answers

### Q1. What is Linux hardening?

Linux hardening is the process of reducing a system's attack surface through secure configuration, restricted access, software updates, monitoring, and other defensive controls.

### Q2. What is the principle of least privilege?

It means giving a user or process only the permissions necessary to perform its intended tasks.

### Q3. How do you identify listening ports in Linux?

Use `ss -tulpen` to inspect listening sockets and associated processes where permissions and system support allow.

### Q4. Why should unnecessary services be disabled?

Unnecessary services increase the attack surface and may introduce avoidable vulnerabilities or exposure.

### Q5. What is the purpose of a host firewall?

It controls network traffic according to defined rules, helping restrict access to permitted services.

### Q6. Why are Linux file permissions important?

They control who can read, modify, execute, or traverse files and directories.

### Q7. What is the risk of a world-writable sensitive file?

An untrusted user may be able to modify its contents, potentially affecting system behavior or security.

### Q8. What are SUID and SGID?

They are special permission bits that affect the effective user or group identity of executables and, in the case of SGID, group inheritance on directories.

### Q9. How would you harden SSH?

Use appropriate authentication, restrict administrative access, disable unnecessary root access, protect keys, verify host identities, update OpenSSH, and monitor authentication events.

### Q10. What is `auditd`?

It is a Linux auditing service that records events selected through audit rules for security monitoring and investigation.

### Q11. What is file integrity monitoring?

It detects changes to selected files or system components by comparing observed state against a trusted baseline or monitoring policy.

### Q12. What should you do before changing a firewall remotely?

Confirm that required management traffic is permitted and that a recovery method, such as console access, is available.

### Q13. Does installing a security scanner make a Linux system secure?

No. The scanner helps identify possible weaknesses, but findings must be validated and appropriate corrective actions must be implemented.

### Q14. What is defense in depth?

It is the use of multiple complementary security controls so that one failed control does not automatically compromise the entire system.

### Q15. How do you verify that hardening worked?

Validate the configuration, test intended access restrictions, verify legitimate functionality, review logs, and repeat relevant security checks.

---

## 22. Knowledge Check

Answer these questions without referring to the previous sections.

1. What is the primary goal of Linux hardening?
2. Why should you establish a security baseline first?
3. What is the difference between stopping and disabling a systemd service?
4. Why is `chmod 777` usually inappropriate for sensitive files?
5. What is the difference between a host firewall and patch management?
6. Why can locking an account password be insufficient to revoke every form of access?
7. What does `ss -tulpen` help you investigate?
8. Why should SSH configuration be validated before reloading the service?
9. How can audit logs support an incident investigation?
10. Why must automated security-audit findings be validated?
11. What is the purpose of file integrity monitoring?
12. Why are tested backups important during incident response?

---

## 23. Summary

Linux hardening combines secure configuration, access control, patch management, network restrictions, service minimization, logging, auditing, and recovery planning.

The correct configuration depends on the system's role. A setting that is appropriate for a standalone workstation may be unsuitable for a server, router, VPN gateway, or container host.

**Key takeaway:** Effective Linux hardening is a continuous process: assess the system, apply justified controls, verify the results, monitor for changes, and maintain a reliable recovery path.

