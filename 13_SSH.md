
# 13. SSH (Secure Shell) for Cybersecurity

## 1. Introduction to SSH

**SSH (Secure Shell)** is a cryptographic network protocol used to securely access and manage remote systems over an unsecured network.

It provides encrypted communication between a client and a server, helping protect credentials, commands, and transmitted data from eavesdropping and tampering.

### Common Use Cases

- Remotely managing Linux servers.
- Secure file transfers using SCP and SFTP.
- Executing commands on remote systems.
- Managing cloud infrastructure and virtual machines.
- Accessing servers during incident response.
- Creating encrypted tunnels for authorized services.
- Automating administrative tasks using SSH keys.

### SSH vs. Telnet

| Feature | SSH | Telnet |
|---|---|---|
| Default port | TCP 22 | TCP 23 |
| Encryption | Yes | No |
| Secure authentication | Yes | Not inherently |
| Data confidentiality | Protected in transit | Exposed in plaintext |
| Recommended for administration | Yes | No |

**Security note:** Avoid Telnet for administrative access across untrusted networks. Use SSH or another appropriately secured management protocol.

---

## 2. How SSH Works

SSH uses a client-server architecture.

- **SSH client:** Initiates a connection to a remote system.
- **SSH server:** Accepts connections and provides remote access according to its configuration and authentication policies.
- **Host key:** Helps the client verify the identity of the server.
- **User authentication:** Verifies the account attempting to log in.
- **Encrypted session:** Protects the communication after key exchange.

### Connection Process

1. The client connects to the server's SSH port.
2. The client and server negotiate protocol algorithms.
3. They perform key exchange to establish shared session keys.
4. The client verifies the server's host key.
5. The user authenticates using a password, public key, or another supported method.
6. An encrypted session is established for the requested operation.

```text
+-------------------+            +-------------------+
|    SSH Client     |            |    SSH Server     |
|                   |            |                   |
| ssh user@host     |----------->| TCP Port 22       |
|                   |            |                   |
| Algorithm         |<---------->| Key Exchange      |
| Negotiation       |            |                   |
|                   |            |                   |
| Verify Host Key   |<---------->| Server Host Key   |
|                   |            |                   |
| User              |---------->| Authentication    |
| Authentication    |            |                   |
|                   |<==========>| Encrypted Session |
+-------------------+            +-------------------+
```

*Conceptual overview: exact negotiation details depend on the SSH implementation and configuration.*

---

## 3. SSH Installation and Service Management

### Debian / Ubuntu / Kali / Parrot OS

Install the OpenSSH client:

```bash
sudo apt update
sudo apt install openssh-client
```

Install the SSH server when remote access to your machine is required:

```bash
sudo apt install openssh-server
```

Check the client version:

```bash
ssh -V
```

Check the server service:

```bash
systemctl status ssh
```

Start the SSH service:

```bash
sudo systemctl start ssh
```

Enable it at boot:

```bash
sudo systemctl enable ssh
```

Restart the service after an approved configuration change:

```bash
sudo systemctl restart ssh
```

Stop the service when remote access is not required:

```bash
sudo systemctl stop ssh
```

**Important:** Starting an SSH server exposes a remote-access service. In a lab, use a private or isolated network and restrict access to trusted systems.

---

## 4. Basic SSH Commands

### Connect to a Remote Host

```bash
ssh username@192.168.1.10
```

Replace the username and address with those of the authorized remote system.

### Connect Using a Non-Default Port

```bash
ssh -p 2222 username@192.168.1.10
```

### Execute a Remote Command

```bash
ssh username@192.168.1.10 "hostname"
```

Check remote uptime:

```bash
ssh username@192.168.1.10 "uptime"
```

Check disk usage:

```bash
ssh username@192.168.1.10 "df -h"
```

### Specify an Identity File

```bash
ssh -i ~/.ssh/id_ed25519 username@192.168.1.10
```

### Enable Verbose Troubleshooting

```bash
ssh -v username@192.168.1.10
```

For additional diagnostic output:

```bash
ssh -vv username@192.168.1.10
```

```bash
ssh -vvv username@192.168.1.10
```

Verbose output can contain usernames, hostnames, paths, and other operational details. Review it before sharing publicly.

---

## 5. SSH Configuration Files

### Client Configuration

Location:

```text
~/.ssh/config
```

This file stores per-host client settings.

Example:

```sshconfig
Host lab-server
    HostName 192.168.1.10
    User analyst
    Port 22
    IdentityFile ~/.ssh/id_ed25519
    IdentitiesOnly yes
```

Connect using the alias:

```bash
ssh lab-server
```

Recommended permissions:

```bash
chmod 700 ~/.ssh
chmod 600 ~/.ssh/config
```

### Server Configuration

Main configuration file:

```text
/etc/ssh/sshd_config
```

It controls server-side SSH behavior.

Inspect effective configuration:

```bash
sudo sshd -T
```

Check configuration syntax before restarting or reloading the service:

```bash
sudo sshd -t
```

If validation succeeds, reload the service:

```bash
sudo systemctl reload ssh
```

If reload is unsupported or a restart is required, use the appropriate service action for your distribution.

**Operational precaution:** Keep an existing administrative session open while changing SSH settings. Verify a second connection before closing the original session to reduce the risk of locking yourself out.

---

## 6. SSH Authentication Methods

### 6.1 Password Authentication

The user enters the account password when prompted.

Advantages:
- Simple to configure.
- Useful for basic labs and initial access.

Risks:
- Weak passwords may be guessed.
- Passwords may be reused across services.
- Exposed login services may attract automated login attempts.

Use strong, unique credentials and additional controls such as network restrictions and rate limiting where appropriate.

### 6.2 Public-Key Authentication

SSH key authentication uses a key pair:

- **Private key:** Kept secret on the client.
- **Public key:** Installed on the remote account.
- **Authentication process:** The client proves possession of the private key without transmitting it as a password.

Generate an Ed25519 key pair:

```bash
ssh-keygen -t ed25519 -C "lab-access"
```

Follow the prompts and preferably protect the private key with a passphrase.

Default files:

```text
~/.ssh/id_ed25519
~/.ssh/id_ed25519.pub
```

Never share the private key.

Display the public key:

```bash
cat ~/.ssh/id_ed25519.pub
```

Copy the public key to an authorized server:

```bash
ssh-copy-id username@192.168.1.10
```

Connect using the key:

```bash
ssh -i ~/.ssh/id_ed25519 username@192.168.1.10
```

Test the connection before disabling password authentication, if that is part of your security plan.

### 6.3 SSH Agent

An SSH agent can hold decrypted private-key material in memory so that the passphrase does not need to be entered repeatedly.

Start an agent in a compatible shell:

```bash
eval "$(ssh-agent -s)"
```

Add a key:

```bash
ssh-add ~/.ssh/id_ed25519
```

List identities:

```bash
ssh-add -l
```

Remove identities from the agent when they are no longer needed:

```bash
ssh-add -D
```

Use agents carefully, especially on shared or untrusted systems.

---

## 7. SSH Key and File Permissions

SSH may reject keys or configuration files when their permissions are too permissive.

Inspect permissions:

```bash
ls -ld ~/.ssh
ls -l ~/.ssh/
```

Set common secure permissions:

```bash
chmod 700 ~/.ssh
chmod 600 ~/.ssh/id_ed25519
chmod 644 ~/.ssh/id_ed25519.pub
chmod 600 ~/.ssh/config
```

The public key can normally be readable by other users; the private key must remain protected.

On the remote server, authorized public keys are commonly stored in:

```text
~/.ssh/authorized_keys
```

Set permissions:

```bash
chmod 700 ~/.ssh
chmod 600 ~/.ssh/authorized_keys
```

Correct ownership also matters. For a user's SSH directory, verify that the directory and its files belong to the intended account.

**Never use `chmod 777` on SSH files.** It creates unnecessary security exposure and can cause SSH to reject the files.

---

## 8. Hardening the SSH Server

SSH hardening reduces the likelihood of unauthorized access and limits the impact of compromised credentials.

Edit the server configuration:

```bash
sudo nano /etc/ssh/sshd_config
```

Potential hardening settings include:

```text
PermitRootLogin no
PubkeyAuthentication yes
PermitEmptyPasswords no
AllowUsers analyst
```

These are examples, not a universal drop-in configuration. Replace `analyst` with an appropriate account and verify that the account has working administrative access before applying restrictions.

### Password Authentication

Where public-key authentication is fully configured and tested, an administrator may choose:

```text
PasswordAuthentication no
```

Do not disable password authentication before verifying that the intended key-based access works.

### Additional Controls

Consider:
- Restricting SSH to trusted source IP addresses.
- Using a VPN or a controlled management network.
- Applying firewall rules.
- Disabling unused accounts.
- Keeping OpenSSH updated.
- Monitoring authentication logs.
- Using multi-factor authentication where supported.
- Limiting administrative access through groups or approved accounts.
- Rotating keys when access is revoked or compromise is suspected.

### Validate and Apply Changes

First, back up the configuration:

```bash
sudo cp /etc/ssh/sshd_config /etc/ssh/sshd_config.backup
```

Validate the syntax:

```bash
sudo sshd -t
```

Inspect the effective settings:

```bash
sudo sshd -T
```

Reload the service:

```bash
sudo systemctl reload ssh
```

Then verify a new connection from a separate terminal.

**Note:** SSH configuration may include additional files through `Include` directives. Inspect those files when diagnosing unexpected settings.

---

## 9. SSH Host-Key Verification

The server's host key helps the client determine whether it is connecting to the expected server.

On first connection, SSH may ask whether to trust the host key.

Do not accept the prompt blindly when connecting to a sensitive system. Verify the fingerprint through a trusted channel, such as your administrator or the server console.

Display a host's key fingerprint from a trusted server console:

```bash
sudo ssh-keygen -lf /etc/ssh/ssh_host_ed25519_key.pub
```

### known_hosts

Client-side known-host records are commonly stored in:

```text
~/.ssh/known_hosts
```

Inspect a matching record:

```bash
ssh-keygen -F 192.168.1.10
```

If a host key changes unexpectedly, stop and investigate. Possible explanations include a legitimate server rebuild, a changed IP-to-host mapping, or a potential interception attempt.

After independently verifying that the change is legitimate, remove the stale entry if necessary:

```bash
ssh-keygen -R 192.168.1.10
```

Reconnect and verify the new fingerprint through a trusted source.

**Security principle:** Do not treat disabling host-key checking as a normal fix for connection warnings.

---

## 10. Secure File Transfer

### SCP

SCP copies files over SSH.

Copy a local file to a remote server:

```bash
scp report.txt username@192.168.1.10:/home/username/
```

Download a remote file:

```bash
scp username@192.168.1.10:/home/username/report.txt .
```

Copy a directory recursively:

```bash
scp -r project/ username@192.168.1.10:/home/username/
```

### SFTP

SFTP provides an interactive secure file-transfer session.

Connect:

```bash
sftp username@192.168.1.10
```

Common commands inside SFTP:

```text
pwd
lpwd
ls
lls
cd /home/username
lcd ~/Downloads
get report.txt
put report.txt
bye
```

- `pwd`: Shows the remote working directory.
- `lpwd`: Shows the local working directory.
- `ls`: Lists remote files.
- `lls`: Lists local files.
- `get`: Downloads a remote file.
- `put`: Uploads a local file.

Use SCP or SFTP only with systems and accounts you are authorized to access.

---

## 11. SSH Tunneling and Port Forwarding

SSH can forward traffic through an encrypted connection. This is useful for securely accessing an authorized internal service.

### 11.1 Local Port Forwarding

Example: a service is reachable from the remote SSH server at `127.0.0.1:8080`. Forward local port `9090` to that service:

```bash
ssh -N -L 9090:127.0.0.1:8080 username@192.168.1.10
```

Then access the service locally at:

```text
http://127.0.0.1:9090
```

The command means:
- `-N`: Do not execute a remote shell command.
- `-L`: Create local port forwarding.
- `9090`: Local listening port.
- `127.0.0.1:8080`: Service address as seen from the remote server.

### 11.2 Remote Port Forwarding

Remote forwarding exposes a local service through a port on the SSH server, subject to server configuration and access controls.

Example:

```bash
ssh -N -R 9090:127.0.0.1:3000 username@192.168.1.10
```

Here, connections to the remote forwarding endpoint are directed toward the client's local service. The actual reachability depends on bind settings and server policy.

### 11.3 Dynamic Port Forwarding

Create a local SOCKS proxy:

```bash
ssh -N -D 1080 username@192.168.1.10
```

Configure a compatible application to use the SOCKS proxy at `127.0.0.1:1080`.

**Security note:** Port forwarding can bypass network segmentation or expose internal services. Use it only when explicitly authorized and monitor forwarding permissions on managed systems.

---

## 12. SSH Logs and Security Monitoring

SSH authentication events can help identify unauthorized access attempts, successful logins, and configuration problems.

### Debian / Ubuntu / Kali / Parrot

Inspect authentication logs where available:

```bash
sudo tail -f /var/log/auth.log
```

Search for SSH-related messages:

```bash
sudo grep -i ssh /var/log/auth.log
```

Find failed authentication attempts:

```bash
sudo grep -i "failed password" /var/log/auth.log
```

Find successful authentication messages:

```bash
sudo grep -i "accepted" /var/log/auth.log
```

### Systems Using systemd Journal

View SSH service logs:

```bash
sudo journalctl -u ssh
```

Follow new messages:

```bash
sudo journalctl -u ssh -f
```

On some distributions, the service is named `sshd`:

```bash
sudo journalctl -u sshd
```

Inspect recent logs:

```bash
sudo journalctl -u ssh --since "1 hour ago"
```

Log locations and message formats vary by distribution and logging configuration.

### What to Investigate

- Repeated failed login attempts.
- Successful logins at unusual times.
- Logins from unfamiliar source IP addresses.
- Unexpected root or privileged-account access.
- New or modified `authorized_keys` files.
- Changes to SSH configuration.
- Unusual port-forwarding activity.
- Authentication events followed by suspicious commands.

A failed login does not automatically prove an attack. Correlate the evidence with account ownership, source addresses, timestamps, and other logs.

---

## 13. SSH Troubleshooting

### Problem 1: Connection Refused

Example:

```text
ssh: connect to host 192.168.1.10 port 22: Connection refused
```

Possible causes:
- SSH server is not running.
- SSH is listening on another port.
- A firewall or network device actively rejects the connection.
- The destination address is incorrect.

Checks on the server:

```bash
sudo systemctl status ssh
sudo ss -tlnp | grep ':22'
```

Verify the address, port, and network configuration.

### Problem 2: Connection Timed Out

Possible causes:
- Incorrect IP address or route.
- Firewall silently dropping traffic.
- Server unavailable.
- Network isolation or routing problems.

Check connectivity:

```bash
ip route
```

Test the TCP port where permitted:

```bash
nc -vz 192.168.1.10 22
```

A failed ping alone does not prove that SSH is unavailable; ICMP may be blocked.

### Problem 3: Permission Denied (publickey)

Possible causes:
- Wrong username.
- Incorrect identity file.
- Public key missing from `authorized_keys`.
- Incorrect file ownership or permissions.
- Server policy does not permit the requested authentication method.

Debug:

```bash
ssh -v -i ~/.ssh/id_ed25519 username@192.168.1.10
```

Check local key permissions and remote account configuration.

### Problem 4: Host Identification Has Changed

Do not immediately delete the old host key or bypass checking.

1. Stop the connection attempt.
2. Verify the server identity with the administrator or trusted console.
3. Compare the new fingerprint.
4. Investigate unexplained changes.
5. Update `known_hosts` only after confirming the change is legitimate.

### Problem 5: SSH Service Fails to Start

Validate the configuration:

```bash
sudo sshd -t
```

Inspect service logs:

```bash
sudo journalctl -u ssh --no-pager -n 50
```

On distributions using `sshd`:

```bash
sudo journalctl -u sshd --no-pager -n 50
```

Correct configuration errors before attempting to restart the service.

---

## 14. Practical Security Labs

Perform these labs only on your own machine, a local virtual machine, or an explicitly authorized test environment.

### Lab 1: Set Up an SSH Server

**Objective:** Enable and verify SSH access on a Linux VM.

1. Install `openssh-server`.
2. Start and enable the SSH service.
3. Check its status.
4. Identify the listening port.
5. Connect from an authorized client.

Commands:

```bash
sudo apt update
sudo apt install openssh-server
sudo systemctl enable --now ssh
systemctl status ssh
sudo ss -tlnp | grep ':22'
```

**Expected result:** The SSH service runs and listens on the configured port.

### Lab 2: Configure Key-Based Authentication

**Objective:** Authenticate using an SSH key.

1. Generate an Ed25519 key pair on the client.
2. Install the public key on the test server.
3. Confirm that key-based login works.
4. Check key and directory permissions.
5. Review verbose client output.

Commands:

```bash
ssh-keygen -t ed25519 -C "ssh-lab"
ssh-copy-id username@192.168.1.10
ssh -v username@192.168.1.10
```

**Expected result:** The client authenticates using the configured key.

### Lab 3: Investigate Authentication Logs

**Objective:** Understand SSH login events.

1. Attempt one login with an intentionally incorrect password on your own lab server.
2. Perform a valid login.
3. Inspect the relevant authentication logs.
4. Compare failed and successful events.
5. Record timestamps and source addresses.

Commands:

```bash
sudo journalctl -u ssh --since "30 minutes ago"
sudo grep -i "failed password" /var/log/auth.log
sudo grep -i "accepted" /var/log/auth.log
```

Some commands may return no output when a distribution uses a different service name or log destination.

**Expected result:** You can distinguish relevant authentication events and explain their meaning.

### Lab 4: Apply SSH Hardening

**Objective:** Reduce unnecessary remote-access exposure.

1. Back up the SSH server configuration.
2. Review root-login and authentication settings.
3. Restrict access to the appropriate user accounts.
4. Validate the configuration.
5. Reload the service.
6. Test a second connection before closing the original session.

Commands:

```bash
sudo cp /etc/ssh/sshd_config /etc/ssh/sshd_config.backup
sudo sshd -t
sudo sshd -T
```

Apply changes only after reviewing their impact and confirming an alternative administrative access path.

**Expected result:** You can explain which settings reduce risk and verify that legitimate access still works.

---

## 15. SSH Incident Response Checklist

If you suspect unauthorized SSH access:

- Preserve relevant authentication and system logs.
- Record suspicious timestamps, accounts, source IPs, and hostnames.
- Check successful logins and privileged sessions.
- Review changes to `authorized_keys` and SSH configuration.
- Investigate unexpected processes, persistence mechanisms, and outbound connections.
- Revoke compromised credentials and keys through an approved process.
- Restrict access or isolate the affected host when warranted.
- Rotate affected secrets from a trusted device.
- Patch the system and review the original access path.
- Document actions and verify recovery.

Do not destroy logs or immediately rebuild a system before collecting evidence, unless urgent containment requirements make that necessary.

---

## 16. Best Practices

1. Use key-based authentication where appropriate.
2. Protect private keys with secure permissions and a passphrase.
3. Verify server host keys.
4. Disable root SSH login when operationally appropriate.
5. Restrict SSH access using firewalls, trusted networks, and account policies.
6. Disable unused accounts and remove obsolete public keys.
7. Keep OpenSSH and the operating system updated.
8. Monitor failed and successful authentication events.
9. Back up SSH configuration before making changes.
10. Validate configuration syntax before reloading the service.
11. Avoid exposing SSH directly to the public internet when safer access methods are available.
12. Never disable host-key verification merely to suppress a warning.
13. Review port-forwarding permissions and usage.
14. Test administrative access after every significant configuration change.

---

## 17. Common SSH Commands Cheat Sheet

| Task | Command |
|---|---|
| Connect to a server | `ssh user@host` |
| Use a custom port | `ssh -p 2222 user@host` |
| Use a private key | `ssh -i ~/.ssh/id_ed25519 user@host` |
| Debug a connection | `ssh -vvv user@host` |
| Generate a key | `ssh-keygen -t ed25519` |
| Copy a public key | `ssh-copy-id user@host` |
| List loaded keys | `ssh-add -l` |
| Check server service | `systemctl status ssh` |
| Check listening ports | `sudo ss -tlnp` |
| Validate server config | `sudo sshd -t` |
| Inspect effective config | `sudo sshd -T` |
| View service logs | `sudo journalctl -u ssh` |
| Copy a file | `scp file.txt user@host:/path/` |
| Start SFTP | `sftp user@host` |
| Create local forwarding | `ssh -L local:remote-host:remote-port user@host` |

---

## 18. Interview Questions and Answers

### Q1. What is SSH?

SSH is a cryptographic protocol used for secure remote access, command execution, and file transfer.

### Q2. What is the default SSH port?

TCP port 22 by default. Administrators can configure another port, but changing the port alone does not provide strong security.

### Q3. What is the difference between an SSH client and server?

The client initiates a connection. The server accepts and handles the connection according to its configuration and authentication policies.

### Q4. What is the difference between a public key and a private key?

The public key can be installed on authorized servers. The private key must remain secret and is used by the client to prove possession of the corresponding key pair.

### Q5. What is `authorized_keys`?

It is a file that commonly contains public keys permitted to authenticate to a particular user account.

### Q6. Why is host-key verification important?

It helps the client verify the identity of the remote server and can reveal unexpected changes that require investigation.

### Q7. What does `ssh -v` do?

It displays diagnostic information that helps troubleshoot connection and authentication problems.

### Q8. How do you check whether SSH is listening?

Use `ss -tlnp` on the server and inspect the listening socket and configured port.

### Q9. How would you investigate repeated failed SSH logins?

Review authentication logs, correlate timestamps and source addresses, check for successful logins, and investigate account or configuration changes.

### Q10. Why should root login often be disabled over SSH?

Direct root login increases the exposure of the most privileged account. Using named accounts with controlled privilege escalation improves accountability and access control.

### Q11. What is SSH tunneling?

It forwards network traffic through an SSH connection, allowing authorized access to services through a local, remote, or dynamic forwarding mechanism.

### Q12. What would you do if the server host key changed unexpectedly?

Stop and verify the server identity through a trusted channel before updating the stored host key. Investigate unexplained changes as a potential security issue.

### Q13. Does changing the default SSH port make a server secure?

No. It may reduce some background scanning noise, but strong authentication, patching, access restrictions, and monitoring remain essential.

### Q14. What is the difference between SCP and SFTP?

Both support secure file transfer over SSH. SCP is commonly used for direct file copying, while SFTP provides an interactive file-transfer protocol with commands for navigating and managing remote files.

### Q15. How do you validate SSH server configuration?

Run `sudo sshd -t` to check configuration syntax before reloading or restarting the service.

---

## 19. Knowledge Check

Try answering these without looking at the earlier sections:

1. Why must an SSH private key remain secret?
2. What is the purpose of `known_hosts`?
3. Which command checks SSH server configuration syntax?
4. What is the risk of disabling password authentication before testing key-based login?
5. How can you inspect failed SSH authentication attempts?
6. What is the difference between local and remote port forwarding?
7. Why is `chmod 777 ~/.ssh` unsafe?
8. What should you do before trusting a changed server host key?
9. Why should SSH logs be preserved during incident response?
10. Why is changing port 22 not a complete security solution?

---

## 20. Summary

SSH is a fundamental tool for secure Linux administration and cybersecurity operations. Understanding authentication, key management, host-key verification, port forwarding, log analysis, and server hardening helps you manage remote systems safely.

**Key takeaway:** Secure SSH access depends on more than encryption. It also requires correct identity verification, strong authentication, restrictive access policies, reliable monitoring, and careful configuration management.

