# 09 — Linux Networking Commands

## 1. Introduction to Linux Networking

**Linux networking** involves configuring, monitoring, troubleshooting, and securing communication between computers, servers, and other network devices.

Linux networking commands help you inspect network interfaces, identify IP addresses, test connectivity, troubleshoot DNS, examine routes, and investigate network connections.

### Why Is Networking Important in Cybersecurity?

Cybersecurity professionals use networking commands to:

- Identify a system's IP addresses and network interfaces.
- Troubleshoot connectivity problems.
- Investigate DNS resolution.
- Examine listening ports and active connections.
- Monitor network traffic.
- Identify unexpected network activity.
- Verify firewall and routing configurations.

---

## 2. Check Your IP Address

An **IP address** identifies a network interface within an IP network.

Linux commonly uses IPv4 and IPv6.

### Command: `ip addr`

```bash
ip addr
```

Displays network interfaces and their IP addresses.

Short version:

```bash
ip a
```

Example output:

```text
2: enp0s3: <BROADCAST,MULTICAST,UP,LOWER_UP>
    inet 10.0.2.15/24
    inet6 fe80::a00:27ff:fe12:3456/64
```

Interpretation:

- `enp0s3` — network interface name.
- `inet` — IPv4 address.
- `inet6` — IPv6 address.
- `/24` — IPv4 prefix length.
- `UP` — interface is administratively enabled.

The example addresses are illustrative; your output will differ.

### Display Only IPv4 Addresses

```bash
ip -4 addr
```

### Display Only IPv6 Addresses

```bash
ip -6 addr
```

### Display Active Network Interfaces

```bash
ip link show
```

**Cybersecurity use:** Identify which interfaces are connected to a network before performing an authorized network investigation.

---

## 3. Understand Network Interfaces

A network interface allows a system to communicate over a network.

Common interface types include:

- Ethernet interfaces.
- Wireless interfaces.
- Loopback interfaces.
- Virtual machine interfaces.
- VPN and tunnel interfaces.

### Display Interfaces

```bash
ip link show
```

### Display Interface Statistics

```bash
ip -s link
```

This shows interface statistics, including transmitted and received packets and errors.

### Check the Loopback Interface

```bash
ip addr show lo
```

The loopback interface is commonly associated with:

```text
127.0.0.1
```

This IPv4 address refers to the local system.

The IPv6 loopback address is:

```text
::1
```

**Important:** Loopback traffic remains within the local host rather than being sent to a remote network.

---

## 4. Check Your Default Gateway and Routing Table

A **default gateway** is the next-hop router used when a destination does not match a more specific route.

### Display the Routing Table

```bash
ip route
```

Example:

```text
default via 10.0.2.2 dev enp0s3
10.0.2.0/24 dev enp0s3 proto kernel scope link
```

Interpretation:

- `default` — the default route.
- `via 10.0.2.2` — the next-hop gateway.
- `dev enp0s3` — the interface used for that route.
- `10.0.2.0/24` — the directly connected network in this example.

### Check the Route to a Specific Destination

```bash
ip route get 8.8.8.8
```

This shows the route Linux would select for that destination.

**Cybersecurity use:** Verify routing paths during network troubleshooting and understand which interface a system uses to reach a destination.

---

## 5. Test Network Connectivity with `ping`

`ping` sends ICMP Echo Request messages and checks for Echo Replies.

### Test Connectivity to an IP Address

```bash
ping 8.8.8.8
```

Press `Ctrl + C` to stop it.

### Send a Limited Number of Requests

```bash
ping -c 4 8.8.8.8
```

### Test a Domain Name

```bash
ping -c 4 example.com
```

This tests both name resolution and ICMP reachability.

### Understand the Results

- **Replies received:** The destination responded to the requests.
- **Packet loss:** Some or all requests did not receive replies.
- **Destination unreachable:** A system or router reported that the destination could not be reached.
- **Unknown host:** Name resolution may have failed.

**Important:** A failed ping does not prove that a system is offline. Firewalls may block ICMP while allowing other traffic.

---

## 6. Trace the Network Path

`traceroute` attempts to identify the routers along a path to a destination.

### Run Traceroute

```bash
traceroute example.com
```

If the command is not installed, install it from a trusted repository:

```bash
sudo apt update
sudo apt install traceroute
```

### Use Tracepath

Some distributions provide `tracepath`:

```bash
tracepath example.com
```

These tools use packet probes with different hop limits to discover intermediate network devices.

### Interpret the Output

A typical result lists numbered hops and response times.

A hop displaying `* * *` may indicate that a router did not respond to the probes, filtered them, or that the response was lost.

**Cybersecurity use:** Troubleshoot routing problems and investigate where network communication may be delayed or filtered.

---

## 7. DNS Lookup Commands

**DNS (Domain Name System)** translates domain names into records such as IP addresses.

For example, DNS can help resolve `example.com` to an IP address.

### Command: `nslookup`

```bash
nslookup example.com
```

### Command: `dig`

```bash
dig example.com
```

Display only the answer section:

```bash
dig example.com +short
```

Query a particular record type:

```bash
dig example.com A
dig example.com AAAA
dig example.com MX
dig example.com NS
```

Common DNS record types:

| Record | Purpose |
|---|---|
| A | Maps a name to an IPv4 address |
| AAAA | Maps a name to an IPv6 address |
| MX | Identifies mail servers for a domain |
| NS | Identifies authoritative name servers |
| CNAME | Provides an alias for another name |
| TXT | Stores text data, often used for domain verification and email security policies |

### If `dig` Is Missing

On Debian-based distributions:

```bash
sudo apt update
sudo apt install dnsutils
```

On some distributions, DNS tools may be packaged under a different package name.

**Cybersecurity use:** DNS tools are useful for troubleshooting resolution problems and examining public DNS records during authorized reconnaissance.

**Note:** DNS records can change, and a DNS answer alone does not establish that a website or IP address is trustworthy.

---

## 8. Check DNS Configuration

Linux systems may obtain DNS settings through NetworkManager, DHCP, systemd-resolved, VPN software, or other configuration mechanisms.

### Inspect the Resolver Configuration

```bash
cat /etc/resolv.conf
```

This file may contain nameserver addresses or point to a local resolver service.

### Check Name Resolution with `getent`

```bash
getent hosts example.com
```

This uses the system's configured name-service mechanisms to look up the host.

### Check `systemd-resolved`

On systems using systemd-resolved:

```bash
resolvectl status
```

This displays DNS configuration information.

**Security use:** Investigate unexpected DNS servers, name-resolution failures, or differences between system DNS settings and VPN requirements.

Do not overwrite DNS configuration files without understanding how your distribution manages them.

---

## 9. Check Open Ports and Network Connections

A **port** is a transport-layer endpoint used by protocols such as TCP and UDP.

Ports help operating systems distinguish between different network services.

### Command: `ss`

`ss` displays socket information.

Show listening TCP and UDP sockets:

```bash
ss -lntup
```

Common options:

- `-l` — listening sockets.
- `-n` — numeric addresses and port numbers.
- `-t` — TCP sockets.
- `-u` — UDP sockets.
- `-p` — associated process information when available and permitted.

### Display TCP Connections

```bash
ss -tn
```

### Display Listening TCP Sockets

```bash
ss -lnt
```

### Display Listening UDP Sockets

```bash
ss -lnu
```

### Check a Particular Port

For example, check whether a local TCP service is listening on port 22:

```bash
ss -lnt | grep ':22'
```

No output does not necessarily mean SSH is unavailable remotely; the service could be using another port or interface, or access may be provided differently.

**Cybersecurity use:** Identify unexpected listening services and investigate the network exposure of your own Linux system.

---

## 10. Understand Common Network Ports

| Port | Common service | Protocol |
|---:|---|---|
| 20/21 | FTP data/control | TCP |
| 22 | SSH | TCP |
| 25 | SMTP | TCP |
| 53 | DNS | TCP/UDP |
| 67/68 | DHCPv4 | UDP |
| 80 | HTTP | TCP |
| 123 | NTP | UDP |
| 143 | IMAP | TCP |
| 443 | HTTPS | TCP |
| 445 | SMB | TCP |
| 3306 | MySQL | TCP |
| 3389 | RDP | TCP |
| 5432 | PostgreSQL | TCP |
| 8080 | Common alternate HTTP port | Usually TCP |

These are common defaults, not guarantees. Services can use other ports, and a port number alone does not prove which service is running.

### Check Whether a TCP Port Is Reachable

If Netcat is installed:

```bash
nc -vz example.com 443
```

This attempts a TCP connection to port 443.

Use connection tests only against systems you own or are authorized to assess.

---

## 11. Check Network Connectivity with `curl`

`curl` transfers data using supported protocols, including HTTP and HTTPS.

### Request HTTP Response Headers

```bash
curl -I https://example.com
```

This requests response headers using an HTTP HEAD request when supported by the server.

### Display Connection and Transfer Details

```bash
curl -v https://example.com
```

Verbose output can help troubleshoot DNS, connection establishment, TLS, and HTTP behavior.

Be careful when sharing verbose logs because they may expose internal addresses, headers, or other sensitive details.

### Check the HTTP Status Code

```bash
curl -s -o /dev/null -w "%{http_code}\n" https://example.com
```

This prints the HTTP response status code.

Common examples:

- `200` — successful response.
- `301` or `302` — redirection.
- `403` — request forbidden.
- `404` — resource not found.
- `500` — server-side error.

A status code does not by itself establish whether a site is secure.

**Security note:** Do not use options that disable TLS certificate verification as a routine workaround. Investigate certificate, trust-store, hostname, and system-time issues instead.

---

## 12. Check the Hostname and Local Network Information

### Display the Hostname

```bash
hostname
```

### Display Detailed System and Hostname Information

```bash
hostnamectl
```

### Display the System's Network Addresses

```bash
hostname -I
```

This commonly displays assigned non-loopback addresses, although the exact output depends on the system.

### Check the ARP/Neighbor Table

```bash
ip neigh
```

This displays known network neighbors and their link-layer address information where available.

For IPv4, this often corresponds to ARP-based neighbor discovery. IPv6 uses Neighbor Discovery Protocol.

**Cybersecurity use:** Inspect local network configuration and neighbor entries during troubleshooting. Neighbor tables are temporary and do not provide a complete inventory of every device on a network.

---

## 13. Monitor Network Traffic with `tcpdump`

`tcpdump` captures and displays packets matching specified filters.

Packet captures can contain sensitive information, so capture only traffic you are authorized to inspect.

### Check Whether `tcpdump` Is Installed

```bash
tcpdump --version
```

If required and available in your distribution:

```bash
sudo apt update
sudo apt install tcpdump
```

### List Available Capture Interfaces

```bash
ip link show
```

Alternatively, use:

```bash
sudo tcpdump -D
```

### Capture Traffic on an Interface

Replace `enp0s3` with the correct interface:

```bash
sudo tcpdump -i enp0s3
```

Stop the capture with `Ctrl + C`.

### Capture a Limited Number of Packets

```bash
sudo tcpdump -i enp0s3 -c 20
```

### Capture DNS Traffic

```bash
sudo tcpdump -i enp0s3 port 53
```

### Capture Traffic for a Specific Host

```bash
sudo tcpdump -i enp0s3 host 192.168.1.10
```

Replace the example address with the authorized target address.

### Save Packets to a File

```bash
sudo tcpdump -i enp0s3 -c 100 -w capture.pcap
```

Read the capture later:

```bash
tcpdump -r capture.pcap
```

Open the `.pcap` file in Wireshark for graphical packet analysis.

**Important:** Capturing traffic may require elevated privileges. Protect capture files because they can contain sensitive metadata and application data.

---

## 14. Check Firewall Rules

A firewall controls network traffic according to configured rules.

Linux systems may use nftables, iptables compatibility tools, UFW, firewalld, or other firewall management systems.

### Check UFW Status

```bash
sudo ufw status verbose
```

UFW may not be installed or enabled.

### List nftables Rules

```bash
sudo nft list ruleset
```

This displays the current nftables ruleset when available and permitted.

### List iptables Rules

```bash
sudo iptables -L -n -v
```

Some systems use iptables compatibility commands backed by nftables.

**Security note:** Inspect the firewall system actually used by your distribution. Do not assume that every Linux system uses UFW or that an empty output from one tool proves there is no firewall.

---

## 15. Check Network Services

Network services may listen for incoming connections or establish outgoing connections.

### Check a Service's Status

For example:

```bash
systemctl status ssh
```

The service may be named differently on some distributions. On Debian-based systems, the OpenSSH server service is commonly named `ssh`.

### Check Listening Services

```bash
sudo ss -lntup
```

### Why This Matters

An unnecessary listening service can increase the system's attack surface.

Before disabling a service, determine its purpose and whether other applications depend on it.

**Best practice:** Disable unnecessary services through the appropriate service-management tool, then verify that legitimate functionality remains intact.

---

## 16. Troubleshoot Common Networking Problems

### Problem 1: No IP Address

Check the interfaces:

```bash
ip addr
```

Check interface state:

```bash
ip link show
```

Check routes:

```bash
ip route
```

Possible causes include a disconnected network adapter, disabled interface, DHCP failure, or incorrect configuration.

Do not assign an arbitrary IP address without checking the network's addressing plan.

### Problem 2: Cannot Reach an IP Address

Test the local network configuration:

```bash
ip route
```

Then test a known, authorized destination:

```bash
ping -c 4 8.8.8.8
```

If replies fail, investigate the interface, gateway, routing, firewall rules, and upstream connectivity. ICMP filtering may also explain the failure.

### Problem 3: IP Connectivity Works but Domain Names Fail

Test DNS resolution:

```bash
getent hosts example.com
```

Then inspect DNS configuration:

```bash
cat /etc/resolv.conf
```

If the system uses systemd-resolved:

```bash
resolvectl status
```

This pattern may indicate a DNS configuration or resolver problem.

### Problem 4: Website Does Not Open

Check DNS:

```bash
getent hosts example.com
```

Check HTTPS connectivity:

```bash
curl -I https://example.com
```

If necessary, inspect the route:

```bash
ip route get 8.8.8.8
```

Possible causes include DNS failures, routing problems, firewall restrictions, TLS errors, or application-server issues.

### Problem 5: A Service Is Not Accessible

Check whether it is listening:

```bash
sudo ss -lntup
```

Inspect the relevant service:

```bash
systemctl status service_name
```

Check firewall rules using the firewall system configured on your machine.

Remember that a service bound only to `127.0.0.1` is listening on loopback rather than on the machine's external network interfaces.

---

## 17. Practical Lab: Linux Network Investigation

**Objective:** Inspect a Linux system's network configuration and troubleshoot basic connectivity.

Use your own virtual machine or a system you are authorized to administer.

### Step 1: Identify Network Interfaces

```bash
ip link show
```

Record the interface names and their states.

### Step 2: Find IP Addresses

```bash
ip addr
```

Identify the IPv4 and IPv6 addresses assigned to each interface.

### Step 3: Inspect the Routing Table

```bash
ip route
```

Identify the default route if one exists.

### Step 4: Test IP Connectivity

```bash
ping -c 4 8.8.8.8
```

Record packet loss and response times.

### Step 5: Test DNS Resolution

```bash
getent hosts example.com
```

Check whether the system resolves the hostname.

### Step 6: Inspect Listening Ports

```bash
sudo ss -lntup
```

Record the listening ports and associated processes where available.

### Step 7: Inspect HTTPS Connectivity

```bash
curl -I https://example.com
```

Record the response status and investigate any errors.

### Step 8: Inspect Neighbor Entries

```bash
ip neigh
```

Review the neighbor entries currently known to the system.

### Lab Questions

1. Which network interface is connected?
2. What IPv4 address is assigned to it?
3. Is a default route configured?
4. Can the system reach the test destination?
5. Does DNS resolution work?
6. Which TCP or UDP ports are listening?
7. Does the HTTPS request receive a response?
8. Which command would you use to investigate each failure?

Document the output and conclusions without exposing passwords, tokens, or other sensitive information.

---

## 18. Quick Revision Cheat Sheet

| Task | Command |
|---|---|
| Show IP addresses | `ip addr` |
| Show interfaces | `ip link show` |
| Show interface statistics | `ip -s link` |
| Show routing table | `ip route` |
| Check selected route | `ip route get 8.8.8.8` |
| Test connectivity | `ping -c 4 8.8.8.8` |
| Trace network path | `traceroute example.com` |
| Look up DNS | `nslookup example.com` |
| Query DNS records | `dig example.com` |
| Use system name resolution | `getent hosts example.com` |
| Show listening sockets | `ss -lntup` |
| Show TCP connections | `ss -tn` |
| Test TCP port | `nc -vz example.com 443` |
| Inspect HTTP headers | `curl -I https://example.com` |
| Show hostname | `hostname` |
| Show local network addresses | `hostname -I` |
| Show neighbor table | `ip neigh` |
| Capture packets | `sudo tcpdump -i enp0s3` |
| Check UFW | `sudo ufw status verbose` |
| Inspect nftables rules | `sudo nft list ruleset` |
| Check a service | `systemctl status service_name` |

---

## 19. Common Interview Questions and Answers

### Q1. What is an IP address?

An IP address is a logical network address used to identify an interface and support communication over an IP network.

### Q2. What is the difference between IPv4 and IPv6?

IPv4 uses 32-bit addresses, while IPv6 uses 128-bit addresses and provides a much larger address space.

### Q3. What is a default gateway?

A default gateway is the next-hop router used when a destination does not match a more specific route.

### Q4. What does the `ip addr` command do?

It displays network interfaces and their assigned IP addresses.

### Q5. What is the purpose of `ping`?

It tests reachability by sending ICMP Echo Requests and checking for replies.

### Q6. Does a failed ping mean the host is offline?

No. The host may be online but configured to block or ignore ICMP requests.

### Q7. What is DNS?

DNS is a system that provides records mapping domain names to information such as IP addresses and mail-server details.

### Q8. What is the difference between `dig` and `nslookup`?

Both can query DNS. `dig` provides detailed DNS query output and supports flexible record queries, while `nslookup` offers a simpler lookup interface.

### Q9. What is the purpose of `ss`?

`ss` displays socket information, including listening sockets and active network connections.

### Q10. What is a listening port?

A listening port is a local transport endpoint waiting for incoming connections or datagrams, depending on the protocol and socket configuration.

### Q11. What is the difference between TCP and UDP?

TCP provides a connection-oriented, reliable byte stream. UDP sends datagrams without providing TCP-style delivery or ordering guarantees.

### Q12. What is `tcpdump` used for?

`tcpdump` captures and displays network packets that match specified filters.

### Q13. What is the purpose of `traceroute`?

It attempts to discover intermediate routers along a network path using packet probes and hop limits.

### Q14. How would you troubleshoot a DNS problem?

Check whether the hostname resolves using `getent hosts` or `dig`, inspect the system's resolver configuration, and verify that the configured DNS server is reachable and working.

### Q15. How would you investigate unexpected open ports?

Use `ss -lntup` to identify listening sockets and associated processes, inspect the relevant services and configuration, and compare the results with the system's expected security baseline.

### Q16. What is the difference between a private IP and a public IP?

A private IP is intended for use within private networks and is not globally routed as a public destination. A public IP is globally routable, subject to routing and filtering policies.

### Q17. What is a firewall?

A firewall applies rules to allow, restrict, or otherwise control network traffic.

### Q18. Why are networking commands important in cybersecurity?

They help professionals investigate connectivity, understand network exposure, inspect traffic, troubleshoot services, and identify suspicious network behavior.

---

## 20. Final Summary

Linux networking commands form a foundation for system administration, network troubleshooting, and cybersecurity investigations.

Remember these essentials:

- Use `ip addr` to inspect IP addresses.
- Use `ip route` to inspect routing.
- Use `ping` to test ICMP reachability.
- Use `dig` or `nslookup` to investigate DNS.
- Use `ss` to inspect listening sockets and connections.
- Use `curl` to troubleshoot HTTP and HTTPS.
- Use `tcpdump` to capture authorized network traffic.
- Inspect firewall rules and services to understand network exposure.
- Interpret command output in context rather than assuming that one failed test proves a system is offline.

**Next file:** `10_Bash_Scripting_Basics.md` — automate repetitive Linux tasks with Bash scripts.
