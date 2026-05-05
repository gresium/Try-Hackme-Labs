# TryHackMe — Pre Security Path Writeup

**Platform:** TryHackMe  
**Path:** [Pre Security](https://tryhackme.com/path/outline/presecurity)  
**Difficulty:** Easy  
**Status:** ✅ Completed  
**Profile:** [gresium](https://tryhackme.com/p/gresium)  
**Date Completed:** April 3, 2026
**Total Rooms:** 31 across 7 sections  

---

## Summary

The Pre Security path covers the foundational knowledge needed before diving into offensive or defensive security work. It starts from the ground up — how computers work internally, how networks communicate, how the web functions — and ends with a look at real-world attack and defence concepts. For anyone already working in pentesting, this path fills in gaps and gives you proper terminology to attach to things you already do intuitively.

---

---

## Section 1 — Introduction to Cyber Security

> Goal: Understand what offensive and defensive security actually mean, and what career paths exist in the field.

---

### Room 1 — Offensive Security Intro
🔗 https://tryhackme.com/room/offensivesecurityintro

**Key Concepts:**
- Offensive security = thinking and acting like an attacker to find vulnerabilities before malicious actors do
- Ethical hacking is legal when done with permission (bug bounties, pentesting contracts, CTFs)
- The room demonstrates a basic web app hack — finding a hidden page and exploiting a fake bank transfer function

**What I did:**
Used GoBuster to brute-force hidden directories on a target web application:
```bash
gobuster dir -u http://fakebank.thm -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt
```
Found a hidden `/bank-transfer` endpoint not linked from the main site. Accessed it directly in the browser and performed an unauthorised transfer to demonstrate the vulnerability.

**Takeaways:**
Security through obscurity (hiding a page but not protecting it with auth) is not security. Even a beginner can find unlinked pages with directory brute-forcing.

---

### Room 2 — Defensive Security Intro
🔗 https://tryhackme.com/room/defensivesecurityintro

**Key Concepts:**
- Defensive security = preventing attacks and detecting/responding when they happen
- Key defensive roles: SOC analysts, threat hunters, DFIR (Digital Forensics & Incident Response), malware analysts
- Tools in the defensive world: SIEM, IDS/IPS, firewalls, antivirus, threat intelligence platforms
- SIEM (Security Information and Event Management) aggregates logs to detect anomalies

**What I did:**
Worked through a simulated SOC analyst scenario using a basic SIEM dashboard. Reviewed alerts, correlated IP addresses against a threat intel feed, identified a malicious login attempt, and escalated the incident. Also looked at a basic malware sample's behaviour without executing it.

**Takeaways:**
Defensive security is just as technical as offensive. SOC work requires reading logs quickly and correlating events across multiple sources — pattern recognition is the core skill.

---

### Room 3 — Careers in Cyber
🔗 https://tryhackme.com/room/careersincyber

**Key Concepts:**
- Security Analyst: monitors networks, triages alerts, writes reports
- Penetration Tester: authorized attacker who finds vulnerabilities
- Red Team: adversary simulation, long-term engagements, evades detection
- Security Engineer: builds and maintains security systems
- DFIR: investigates breaches after the fact
- Malware Analyst: reverse engineers malicious software
- Each role has different day-to-day tasks and required skill sets

**What I did:**
Read through role descriptions and matched skills/interests to career paths. Completed the quiz questions on which role handles what type of task.

**Takeaways:**
Penetration testing and red teaming are different — red team engagements simulate a full APT (Advanced Persistent Threat) with stealth and persistence goals, while pentesting is typically more structured and time-boxed.

---

---

## Section 2 — Network Fundamentals

> Goal: Understand how devices communicate — from local networks all the way up to the protocol stack.

---

### Room 4 — What is Networking?
🔗 https://tryhackme.com/room/whatisnetworking

**Key Concepts:**
- A network is any collection of devices that can communicate with each other
- The internet is a massive network of networks
- IP addresses identify devices on a network (IPv4: 4 octets, e.g. 192.168.1.1)
- MAC addresses identify the physical hardware (48-bit, written as 6 pairs of hex, e.g. `a4:c3:f0:85:ac:2d`)
- Ping uses ICMP to test reachability between devices

**What I did:**
Used `ping` to test connectivity between machines. Reviewed how IP and MAC addresses differ — IP is logical and can change, MAC is burned into the hardware. Traced the concept of how packets are routed across multiple hops to reach a destination.

```bash
ping 8.8.8.8
```

**Takeaways:**
MAC addresses operate at Layer 2 (Data Link), IP addresses at Layer 3 (Network). Knowing which layer a problem lives on is the fastest way to debug network issues.

---

### Room 5 — Intro to LAN
🔗 https://tryhackme.com/room/introtolan

**Key Concepts:**
- LAN (Local Area Network): devices connected in the same physical or logical space
- Network topologies: Star (most common — all devices connect to a central switch), Bus (single cable, legacy), Ring (circular path, legacy)
- Switch: connects devices within a LAN, uses MAC address table to forward frames
- Router: connects networks together (LAN to LAN, LAN to internet), works at Layer 3
- Subnetting: divides a network into smaller segments using subnet masks (e.g. `/24` = 255.255.255.0 = 254 usable hosts)
- CIDR notation: `192.168.1.0/24`

**What I did:**
Worked through subnet calculations — given an IP and subnet mask, identified the network address, broadcast address, and usable host range. Reviewed the difference between switches and routers and matched topologies to their diagrams.

**Takeaways:**
Star topology dominates modern networks because a single cable failure only affects one device. Subnetting is a skill that comes up constantly in pentesting — knowing which subnet a target is on matters for enumeration.

---

### Room 6 — OSI Model
🔗 https://tryhackme.com/room/osimodelzi

**Key Concepts:**
- OSI = Open Systems Interconnection, a 7-layer model for how data travels across a network
- Layers (top to bottom):
  - 7 — Application (HTTP, FTP, DNS, SMTP)
  - 6 — Presentation (encryption, encoding, compression)
  - 5 — Session (managing connections between applications)
  - 4 — Transport (TCP/UDP, port numbers, segmentation)
  - 3 — Network (IP addressing, routing)
  - 2 — Data Link (MAC addresses, frames, switches)
  - 1 — Physical (cables, signals, NICs)
- Data is encapsulated as it moves down the stack, decapsulated going up
- PDU names by layer: Data → Segment → Packet → Frame → Bits

**What I did:**
Matched protocols to their correct OSI layers. Traced a web request from browser (Layer 7) all the way down to the physical transmission and back up on the receiving end. Answered questions on which layer handles encryption (6), port numbers (4), and routing (3).

**Takeaways:**
In pentesting, most attacks target Layer 7 (web app attacks), Layer 4 (port scanning, firewall bypasses), or Layer 3 (routing attacks). Understanding which layer a tool operates at helps you understand what it can and cannot see.

---

### Room 7 — Packets & Frames
🔗 https://tryhackme.com/room/packetsframes

**Key Concepts:**
- Packet: Layer 3 unit, contains source/destination IP
- Frame: Layer 2 unit, contains source/destination MAC
- TCP (Transmission Control Protocol): connection-oriented, reliable, ordered — uses 3-way handshake (SYN → SYN-ACK → ACK)
- UDP (User Datagram Protocol): connectionless, no guarantee of delivery, faster — used for DNS, VoIP, streaming
- TCP headers include: source port, destination port, sequence number, acknowledgement number, flags (SYN, ACK, FIN, RST)
- Ports: well-known (0–1023), registered (1024–49151), dynamic/ephemeral (49152–65535)

**What I did:**
Traced a TCP handshake step-by-step. Matched protocols (DNS, HTTP, FTP, SSH) to whether they use TCP or UDP and to their default port numbers. Reviewed why sequence numbers matter for reassembling out-of-order packets.

**Takeaways:**
TCP's 3-way handshake is the basis of many attacks — SYN floods abuse the half-open connection state. RST packets are used by firewalls and IDS to kill unwanted connections. These concepts come up constantly in traffic analysis and network exploitation.

---

### Room 8 — Extending Your Network
🔗 https://tryhackme.com/room/extendingyournetwork

**Key Concepts:**
- Port forwarding: maps an external port to an internal device/port, exposing internal services externally
- Firewalls: filter traffic based on rules — stateful firewalls track connection state, stateless check each packet in isolation
- VPN (Virtual Private Network): creates an encrypted tunnel between two endpoints, extends a private network over the internet
- VPN types: site-to-site (office-to-office), remote access (user-to-network), SSL VPN (browser-based)
- NAT (Network Address Translation): allows multiple private IPs to share a single public IP

**What I did:**
Configured port forwarding rules in a simulated router interface — mapped external port 80 to an internal web server at 192.168.1.10:80. Reviewed firewall rule logic (ALLOW/DENY based on source IP, destination port, protocol). Traced how NAT allows a home network with private IPs to access the internet through one public IP.

**Takeaways:**
Port forwarding is a common finding in pentests — misconfigured rules expose internal services. Stateful firewalls are much harder to bypass than stateless ones because they track whether a connection was initiated from inside or outside.

---

---

## Section 3 — How The Web Works

> Goal: Understand the technology stack behind websites — from DNS resolution to HTTP communication to how pages are built.

---

### Room 9 — DNS in Detail
🔗 https://tryhackme.com/room/dnsindetail

**Key Concepts:**
- DNS (Domain Name System): translates human-readable domain names to IP addresses
- DNS record types:
  - A: domain → IPv4
  - AAAA: domain → IPv6
  - CNAME: alias → another domain
  - MX: mail server for a domain
  - TXT: arbitrary text (used for SPF, DKIM, domain verification)
  - NS: nameservers for a domain
- DNS resolution order: local cache → /etc/hosts → recursive resolver → root nameserver → TLD nameserver → authoritative nameserver
- TTL (Time To Live): how long a DNS record is cached (in seconds)

**What I did:**
Used `nslookup` and manual queries to resolve domains, look up specific record types, and trace the full resolution chain. Queried MX records to identify mail servers and TXT records for SPF configurations.

```bash
nslookup -type=A tryhackme.com
nslookup -type=MX tryhackme.com
nslookup -type=TXT tryhackme.com
```

**Takeaways:**
DNS is a goldmine during recon — MX records reveal mail infrastructure, TXT records reveal third-party services, NS records reveal registrar and hosting relationships. DNS cache poisoning and subdomain enumeration are attack surfaces that stem directly from how DNS works.

---

### Room 10 — HTTP in Detail
🔗 https://tryhackme.com/room/httpindetail

**Key Concepts:**
- HTTP (HyperText Transfer Protocol): application-layer protocol for web communication
- HTTP methods: GET (retrieve), POST (submit data), PUT (update), DELETE (remove), PATCH (partial update)
- HTTP status codes:
  - 2xx: success (200 OK, 201 Created)
  - 3xx: redirect (301 Moved Permanently, 302 Found)
  - 4xx: client error (400 Bad Request, 401 Unauthorized, 403 Forbidden, 404 Not Found)
  - 5xx: server error (500 Internal Server Error)
- HTTP headers: Host, User-Agent, Content-Type, Cookie, Authorization, Referer
- HTTPS = HTTP + TLS encryption (port 443 vs HTTP port 80)
- Cookies: key-value pairs set by the server, stored by the browser, sent with every subsequent request

**What I did:**
Made manual HTTP requests using curl and observed raw request/response headers. Modified headers manually to test how the server responds to different User-Agent strings and cookie values.

```bash
curl -v http://target.thm/
curl -X POST -d "username=admin&password=test" http://target.thm/login
curl -H "Cookie: admin=true" http://target.thm/dashboard
```

**Takeaways:**
HTTP is stateless by design — cookies are how state is maintained across requests. This is why cookie manipulation, session hijacking, and CSRF all work. Understanding raw HTTP is prerequisite knowledge for any web application testing.

---

### Room 11 — How Websites Work
🔗 https://tryhackme.com/room/howwebsiteswork

**Key Concepts:**
- Websites = HTML (structure) + CSS (styling) + JavaScript (behaviour)
- HTML tags: `<head>`, `<body>`, `<form>`, `<input>`, `<script>`, `<img>`
- JavaScript runs in the browser — can manipulate the DOM, send requests, and validate input
- Sensitive data should never be stored client-side (in HTML source or JS variables)
- Developer tools (F12) expose page source, network requests, cookies, and JavaScript

**What I did:**
Viewed page source of target pages to find hidden comments, hardcoded credentials, and API keys left by developers. Used browser DevTools to inspect JavaScript files and find variables containing sensitive values. Modified form input fields by editing the HTML directly in DevTools to bypass client-side validation.

**Takeaways:**
Client-side validation is cosmetic — it can always be bypassed via DevTools or a proxy. Never trust anything that comes from the browser. Hidden HTML comments and JS source files are common sources of leaked credentials in real web app assessments.

---

### Room 12 — Putting it all together
🔗 https://tryhackme.com/room/puttingitalltogether

**Key Concepts:**
- Full request lifecycle: DNS resolution → TCP connection → HTTP request → server processing → HTTP response → browser rendering
- Load balancers distribute traffic across multiple servers
- CDNs (Content Delivery Networks) cache content closer to users
- Databases (MySQL, PostgreSQL, MongoDB) store dynamic content behind web apps
- WAF (Web Application Firewall) filters malicious HTTP traffic before it reaches the server

**What I did:**
Traced a complete web request from typing a URL in the browser to the rendered page arriving back. Matched each component (DNS, TCP, HTTP, server, database, response) to its role in the chain. Answered questions on what happens at each step and which component handles which function.

**Takeaways:**
Understanding the full stack matters for web pentesting because vulnerabilities can exist at any layer — DNS hijacking, TLS stripping, SQL injection at the DB layer, XSS at the rendering layer. Knowing where you are in the stack tells you what attacks are relevant.

---

---

## Section 4 — Computer Fundamentals

> Goal: Understand how computers work internally — hardware components, virtualisation, and cloud infrastructure.

---

### Room 13 — Inside a Computer System
🔗 https://tryhackme.com/room/insideacomputer

**Key Concepts:**
- CPU (Central Processing Unit): executes instructions — speed measured in GHz, cores allow parallelism
- RAM (Random Access Memory): fast, volatile short-term storage — data lost on power off
- Storage: HDD (magnetic, slower, larger capacity) vs SSD (flash, faster, more expensive per GB)
- Motherboard: connects all components via buses
- GPU: handles graphical processing — also used for parallel compute tasks (ML, crypto)
- PSU: converts AC power to DC for internal components
- Data units: bit → byte → KB → MB → GB → TB

**What I did:**
Matched hardware components to their function. Reviewed how data flows between CPU, RAM, and storage — the CPU fetches instructions from RAM, which was loaded from disk. Answered questions on component specifications.

**Takeaways:**
In forensics and malware analysis, understanding volatile vs non-volatile storage matters — RAM contains running processes and decrypted data that disappears on shutdown. Memory forensics (e.g. with Volatility) captures what was in RAM before power loss.

---

### Room 14 — Computer Types
🔗 https://tryhackme.com/room/computertypes

**Key Concepts:**
- Personal computers (desktops, laptops): general-purpose
- Servers: high uptime, often no display, optimised for handling requests from many clients
- Embedded systems: purpose-built hardware running fixed software (routers, IoT devices, PLCs)
- Mainframes: large-scale, high-reliability systems used in banking and enterprise
- Supercomputers: extreme parallel processing for scientific computation
- Mobile devices: ARM-based, battery-optimised, different security model than desktop

**What I did:**
Categorised real-world examples of devices into their correct computer type. Reviewed why embedded systems are frequently vulnerable — they often run old firmware with no update mechanism.

**Takeaways:**
IoT and embedded systems are a significant attack surface in modern pentesting. They often run outdated Linux kernels, have hardcoded credentials, and expose services with no authentication. OT/ICS environments (factories, utilities) use these at scale.

---

### Room 15 — Client-Server Basics
🔗 https://tryhackme.com/room/clientserverbasics

**Key Concepts:**
- Client-server model: client requests, server responds
- Examples: browser (client) → web server, email client → mail server, game client → game server
- Servers listen on specific ports for incoming connections
- The client initiates the connection, the server accepts it
- This model underlies almost all networked applications

**What I did:**
Traced how a browser makes a request to a web server — the client opens a TCP connection to port 80/443, sends an HTTP GET request, the server processes it and returns an HTTP response. Extended this to other protocols (SSH, FTP, SMTP).

**Takeaways:**
From a pentesting perspective, identifying what services a server is running (i.e., what it's listening on) is the first step in enumeration. Every open port is a potential attack surface.

---

### Room 16 — Virtualisation Basics
🔗 https://tryhackme.com/room/virtualisationbasics

**Key Concepts:**
- Virtualisation: running multiple OS instances on a single physical machine using a hypervisor
- Type 1 hypervisor (bare-metal): runs directly on hardware (VMware ESXi, Hyper-V, Proxmox)
- Type 2 hypervisor (hosted): runs on top of an OS (VirtualBox, VMware Workstation)
- VM (Virtual Machine): isolated environment with its own virtual CPU, RAM, storage, and network interfaces
- Snapshots: saved VM state that can be restored — useful for malware analysis and lab work
- Containers (Docker): lighter than VMs — share the host kernel but isolate user space

**What I did:**
Reviewed the architecture of Type 1 vs Type 2 hypervisors and matched real products to each type. Compared VMs and containers on isolation level, performance overhead, and use case.

**Takeaways:**
VMs are the backbone of CTF labs (including TryHackMe's AttackBox). Container escapes and VM escapes are real attack techniques — breaking out of a sandboxed environment is a high-severity finding in cloud pentesting.

---

### Room 17 — Cloud Computing Fundamentals
🔗 https://tryhackme.com/room/cloudcomputingfundamentals

**Key Concepts:**
- Cloud service models:
  - IaaS (Infrastructure as a Service): raw compute, storage, networking (AWS EC2, Azure VMs)
  - PaaS (Platform as a Service): managed runtime environment (Heroku, Google App Engine)
  - SaaS (Software as a Service): fully managed application (Gmail, Salesforce, Office 365)
- Deployment models: public cloud, private cloud, hybrid cloud
- Shared responsibility model: cloud provider secures the infrastructure; customer secures what they deploy on it
- Key providers: AWS, Azure, GCP
- Common cloud services: object storage (S3), compute (EC2), databases (RDS), identity (IAM)

**What I did:**
Matched service examples to IaaS/PaaS/SaaS categories. Reviewed the shared responsibility model — understanding that a misconfigured S3 bucket is the customer's fault, not AWS's, even if the data leaks.

**Takeaways:**
Cloud misconfigurations are one of the most common real-world vulnerabilities — public S3 buckets, overly permissive IAM roles, exposed metadata endpoints (SSRF to 169.254.169.254). Cloud security is a major growth area in pentesting.

---

---

## Section 5 — Operating Systems Basics

> Goal: Get comfortable with Linux and Windows from the command line up — filesystems, permissions, processes, and security concepts.

---

### Room 18 — Operating Systems: Introduction
🔗 https://tryhackme.com/room/operatingsystemsintroduction

**Key Concepts:**
- An OS manages hardware resources and provides an interface for applications
- Kernel: core of the OS — manages CPU scheduling, memory, I/O
- User space vs kernel space: applications run in user space; the kernel runs in kernel space with full hardware access
- Common OS families: Windows (NT kernel), Linux (monolithic kernel), macOS (XNU kernel)
- Processes: running instances of programs — each has a PID, parent PID, memory space
- File systems: how data is organised on storage — NTFS (Windows), ext4 (Linux), APFS (macOS)

**What I did:**
Reviewed how the OS acts as an intermediary between hardware and software. Traced the boot process from BIOS/UEFI → bootloader → kernel → init system (systemd on Linux, wininit on Windows). Answered questions on process management and filesystem hierarchy.

**Takeaways:**
Privilege escalation in pentesting is fundamentally about moving from user space to kernel space — exploiting the boundary between the two. Understanding how the kernel manages permissions is prerequisite knowledge for privesc techniques.

---

### Room 19 — Windows Basics
🔗 https://tryhackme.com/room/windowsbasics

**Key Concepts:**
- Windows filesystem: C:\ as root, key directories: `C:\Windows\System32`, `C:\Users`, `C:\Program Files`
- NTFS permissions: Full Control, Modify, Read & Execute, Read, Write — applied to users and groups
- Registry: hierarchical database storing OS and application configuration — hives: HKLM, HKCU, HKCR
- Task Manager / Process Explorer: view running processes, CPU/RAM usage
- Services: background processes managed by the Service Control Manager (SCM)
- UAC (User Account Control): prompts for elevation when admin rights are needed
- Windows user accounts: Administrator, Standard User, Guest; domain accounts via Active Directory

**What I did:**
Navigated the Windows filesystem via Explorer and PowerShell. Viewed running processes and services. Inspected the registry for common configuration keys. Reviewed NTFS permissions on files and folders.

**Takeaways:**
The Windows Registry is a critical target in pentesting — credentials, autorun entries, and configuration weaknesses are frequently found there. Services running as SYSTEM with weak binary permissions are a classic privesc path.

---

### Room 20 — Linux CLI Basics
🔗 https://tryhackme.com/room/linuxclibasics

**Key Concepts:**
- Linux filesystem hierarchy: `/` (root), `/home`, `/etc`, `/var`, `/tmp`, `/bin`, `/usr`, `/proc`
- File permissions: read (r=4), write (w=2), execute (x=1) — applied to owner, group, others
- `chmod`, `chown`: change permissions and ownership
- SUID bit: allows a file to run with the owner's permissions regardless of who executes it
- Key commands: `ls`, `cd`, `cat`, `grep`, `find`, `cp`, `mv`, `rm`, `mkdir`, `nano`, `echo`, `pwd`
- Piping and redirection: `|`, `>`, `>>`, `<`
- Background processes: `&`, `jobs`, `fg`, `bg`

**What I did:**
Navigated the filesystem, read files with `cat` and `less`, searched for content with `grep`, found files with `find`. Set and interpreted file permissions using `ls -la` and `chmod`. Used pipes to chain commands.

```bash
ls -la /home/
cat /etc/passwd
find / -name "*.txt" 2>/dev/null
grep -r "password" /var/www/ 2>/dev/null
chmod +x script.sh
find / -perm -4000 2>/dev/null   # find SUID binaries
```

**Takeaways:**
`find / -perm -4000` is one of the first commands in any Linux privesc enumeration — SUID binaries that shouldn't have the bit set are a common escalation path. Knowing the filesystem layout tells you where to look for sensitive files like `/etc/shadow`, SSH keys, and config files.

---

### Room 21 — Windows CLI Basics
🔗 https://tryhackme.com/room/windowsclibasics

**Key Concepts:**
- CMD (Command Prompt): legacy Windows CLI
- PowerShell: modern shell with object-based pipeline and .NET integration
- Key CMD commands: `dir`, `cd`, `type`, `copy`, `del`, `ipconfig`, `netstat`, `tasklist`, `systeminfo`
- Key PowerShell commands: `Get-ChildItem`, `Get-Content`, `Set-Location`, `Get-Process`, `Invoke-WebRequest`
- PowerShell is more powerful for automation and scripting than CMD
- Execution policy: controls whether PS scripts can run (`Get-ExecutionPolicy`, `Set-ExecutionPolicy`)

**What I did:**
Used both CMD and PowerShell to navigate the filesystem, view file contents, list processes, and check network connections. Ran basic PowerShell commands and compared them to their CMD equivalents.

```cmd
dir C:\Users\
type C:\Users\user\Desktop\flag.txt
ipconfig /all
netstat -ano
tasklist
systeminfo
```
```powershell
Get-ChildItem C:\Users\
Get-Content C:\Users\user\Desktop\flag.txt
Get-Process
Get-NetIPAddress
```

**Takeaways:**
PowerShell is the primary tool for post-exploitation on Windows — it can download files, enumerate AD, and execute code in memory. Defenders look at PowerShell logs (`ScriptBlockLogging`), which is why attackers use obfuscation. Knowing both CMD and PS is essential.

---

### Room 22 — Operating System Security
🔗 https://tryhackme.com/room/operatingsystemsecurity

**Key Concepts:**
- Authentication: verifying who you are — passwords, MFA, biometrics
- Principle of least privilege: users and processes should have only the access they need
- Password security: hashing (not storing plaintext), salting to prevent rainbow table attacks
- Common OS hardening steps: disable unnecessary services, apply patches, configure firewall, enable logging
- `sudo` on Linux: run commands as root without logging in as root — logs to `/var/log/auth.log`
- Windows Event Log: records security, system, and application events — Event IDs like 4624 (logon), 4625 (failed logon), 4688 (process creation)

**What I did:**
Reviewed OS hardening checklists. Traced how `sudo` works and what `/etc/sudoers` controls. Looked at Windows Event Logs and identified key event IDs used in threat detection. Reviewed how password hashes work — why MD5 hashes are weak and bcrypt is stronger.

**Takeaways:**
`/etc/sudoers` misconfigurations are one of the most common Linux privesc vectors — entries like `NOPASSWD` or wildcard commands give easy elevation. On Windows, Event ID 4625 (failed logon) spikes during brute-force attacks and is one of the first things a SOC analyst checks.

---

---

## Section 6 — Software Basics

> Goal: Understand how data is represented and encoded, get a first look at coding, and learn database fundamentals.

---

### Room 23 — Data Representation
🔗 https://tryhackme.com/room/datarepresentation

**Key Concepts:**
- Binary (base 2): computers store everything as 0s and 1s
- Decimal (base 10): human-readable number system
- Hexadecimal (base 16): compact representation of binary — 0–9 + A–F, 1 hex digit = 4 bits
- Conversion: binary ↔ decimal ↔ hex
- 1 byte = 8 bits, represented as 2 hex digits (e.g. `0xFF` = 255 in decimal = 11111111 in binary)
- ASCII: maps numbers to characters (e.g. `A` = 65 = 0x41)
- Unicode: extended character encoding supporting international scripts

**What I did:**
Converted between binary, decimal, and hex by hand and using Python. Decoded ASCII values back to characters. Verified understanding by converting IP addresses and MAC addresses between formats.

```python
# Decimal to binary
bin(255)        # '0b11111111'
# Decimal to hex
hex(255)        # '0xff'
# Binary to decimal
int('11111111', 2)  # 255
# ASCII
chr(65)         # 'A'
ord('A')        # 65
```

**Takeaways:**
Hex is everywhere in security — memory addresses, shellcode, hash values, packet captures. Being able to read and convert hex quickly without tools is a baseline skill for exploit development and forensics.

---

### Room 24 — Data Encoding
🔗 https://tryhackme.com/room/dataencoding

**Key Concepts:**
- Encoding ≠ encryption — encoding is reversible without a key, encryption requires a key
- Base64: encodes binary data as ASCII text — used in HTTP headers, email attachments, JWTs
  - `=` padding at the end is a giveaway
- URL encoding: encodes special characters in URLs — space = `%20`, `&` = `%26`
- HTML encoding: encodes characters for safe rendering — `<` = `&lt;`, `>` = `&gt;`
- XOR: simple encoding using bitwise XOR — also the basis for many stream ciphers

**What I did:**
Decoded and encoded strings in Base64, URL encoding, and HTML encoding. Identified encoded payloads in HTTP requests and decoded them. XOR'd a string with a single-byte key.

```bash
echo "Hello World" | base64             # SGVsbG8gV29ybGQ=
echo "SGVsbG8gV29ybGQ=" | base64 -d    # Hello World
python3 -c "import urllib.parse; print(urllib.parse.unquote('%48%65%6c%6c%6f'))"
```

**Takeaways:**
Attackers encode payloads to bypass WAFs and input filters. Recognising Base64 on sight (look for `==` padding, only alphanumeric + `/` + `+`) is a useful skill. JWT tokens are three Base64-encoded sections separated by dots.

---

### Room 25 — Python: Simple Demo
🔗 https://tryhackme.com/room/pythonsimpledemo

**Key Concepts:**
- Python basics: variables, strings, integers, lists, dictionaries, conditionals, loops, functions
- Python is the dominant scripting language in security tooling
- `print()`, `input()`, `len()`, `range()`, string methods (`.upper()`, `.split()`, `.replace()`)
- File I/O: `open()`, `.read()`, `.write()`
- Modules: `import os`, `import sys`, `import requests`

**What I did:**
Wrote basic Python scripts — a simple calculator, a string reverser, and a file reader. Used `requests` to make an HTTP GET request and print the response body.

```python
import requests
r = requests.get("http://target.thm/")
print(r.status_code)
print(r.text)

# Read a file
with open("passwords.txt", "r") as f:
    for line in f:
        print(line.strip())
```

**Takeaways:**
Python is the language of choice for writing quick exploit scripts, automation, and custom tools in CTFs and real assessments. Even a basic level of Python (loops, file I/O, requests) is enough to automate repetitive recon tasks.

---

### Room 26 — JavaScript: Simple Demo
🔗 https://tryhackme.com/room/javascriptsimpledemo

**Key Concepts:**
- JavaScript runs in the browser (client-side) and on servers via Node.js
- Variables: `var`, `let`, `const`
- Functions, conditionals, loops — similar syntax to other C-style languages
- DOM manipulation: `document.getElementById()`, `.innerHTML`, `.style`
- `fetch()` and `XMLHttpRequest`: make HTTP requests from JavaScript
- `console.log()`: print to browser developer console

**What I did:**
Wrote basic JavaScript in the browser console — manipulated DOM elements, read cookie values, and made a fetch request to an API endpoint. Reviewed how `localStorage` and `sessionStorage` store data in the browser.

```javascript
// Read cookies
document.cookie

// Change page content
document.getElementById("username").innerHTML = "admin"

// Make a request
fetch("/api/user").then(r => r.json()).then(data => console.log(data))
```

**Takeaways:**
JavaScript is the language of XSS payloads. Understanding how JS accesses cookies (`document.cookie`), makes requests (`fetch`), and reads storage helps you understand what a successful XSS can actually steal or do.

---

### Room 27 — Database SQL Basics
🔗 https://tryhackme.com/room/databasesqlbasics

**Key Concepts:**
- Database: organised collection of structured data
- SQL (Structured Query Language): used to interact with relational databases (MySQL, PostgreSQL, SQLite, MSSQL)
- Core SQL statements: SELECT, INSERT, UPDATE, DELETE
- WHERE clause: filters results
- JOIN: combines rows from multiple tables based on a related column
- Tables have columns (fields) and rows (records)
- Primary key: unique identifier for each row
- Common web app architecture: frontend → backend code → SQL database

**What I did:**
Wrote and ran SQL queries against a sample database — selected records with WHERE clauses, inserted new rows, updated existing data, and deleted records. Joined two tables to retrieve related data.

```sql
SELECT * FROM users;
SELECT username, password FROM users WHERE id = 1;
SELECT users.username, orders.item FROM users JOIN orders ON users.id = orders.user_id;
INSERT INTO users (username, password) VALUES ('test', 'test123');
UPDATE users SET password = 'newpass' WHERE username = 'admin';
DELETE FROM users WHERE id = 5;
```

**Takeaways:**
SQL injection works by breaking out of the intended query and injecting attacker-controlled SQL. Understanding normal SQL syntax is required to understand how injections work — you can't craft `' OR 1=1 --` meaningfully without knowing what it does to the WHERE clause.

---

---

## Section 7 — Attacks and Defenses

> Goal: Apply everything from previous sections — understand core security principles, cryptography, and what attackers and defenders actually do in practice.

---

### Room 28 — The CIA Triad
🔗 https://tryhackme.com/room/theciatriad

**Key Concepts:**
- CIA Triad = Confidentiality, Integrity, Availability — the three core properties of information security
- Confidentiality: data is only accessible to authorised parties — protected by encryption, access controls, authentication
- Integrity: data is accurate and unmodified — protected by hashing, digital signatures, checksums
- Availability: systems and data are accessible when needed — protected by redundancy, backups, DDoS mitigation
- Attacks target one or more of these:
  - Data breach → Confidentiality
  - SQL injection modifying data → Integrity
  - Ransomware / DDoS → Availability
- PARKERIAN HEXAD extends CIA with Possession, Authenticity, Utility

**What I did:**
Categorised real-world attack scenarios (ransomware, data exfiltration, website defacement, DDoS) against the CIA property they violate. Reviewed how different security controls map to each property.

**Takeaways:**
Risk assessments and security controls always map back to CIA. When writing a pentest report finding, classifying which property is at risk (and the impact to the business if violated) is what makes findings actionable.

---

### Room 29 — Cryptography Concepts
🔗 https://tryhackme.com/room/cryptographyconcepts

**Key Concepts:**
- Encryption: transforms plaintext into ciphertext using an algorithm and key
- Symmetric encryption: same key for encryption and decryption (AES, DES) — fast, used for bulk data
- Asymmetric encryption: public key encrypts, private key decrypts (RSA, ECC) — slower, used for key exchange and signatures
- Hashing: one-way function producing a fixed-length digest (MD5, SHA-1, SHA-256, bcrypt)
  - Used for password storage, file integrity, digital signatures
  - Not encryption — cannot be reversed
- TLS (Transport Layer Security): uses asymmetric crypto for key exchange, then symmetric for the session
- Digital signatures: hash of data encrypted with private key — verifies authenticity and integrity
- Common algorithms: AES-256 (symmetric), RSA-2048/4096 (asymmetric), SHA-256 (hashing)

**What I did:**
Encrypted and decrypted data using symmetric (AES) and asymmetric (RSA) concepts in the room's interactive tasks. Generated hashes of strings and verified that even a single character change produces a completely different hash. Traced the TLS handshake.

```bash
# Hash a file
sha256sum file.txt
md5sum file.txt

# OpenSSL examples
openssl enc -aes-256-cbc -in plaintext.txt -out encrypted.bin -k password
openssl enc -d -aes-256-cbc -in encrypted.bin -out decrypted.txt -k password
```

**Takeaways:**
MD5 and SHA-1 are broken for security use — collision attacks exist. Password hashes should use bcrypt, scrypt, or Argon2 with salting. In pentesting, cracking hashes (with hashcat/john) and identifying weak algorithms in TLS configurations are standard tasks.

---

### Room 30 — Become a Hacker
🔗 https://tryhackme.com/room/becomeahacker

**Key Concepts:**
- Ethical hacking phases: Reconnaissance → Scanning → Gaining Access → Maintaining Access → Covering Tracks
- Reconnaissance types: passive (OSINT, no direct contact with target) vs active (port scans, direct interaction)
- Common tools: nmap (scanning), Metasploit (exploitation framework), Burp Suite (web proxy), Wireshark (packet capture)
- Bug bounty programs: companies pay for vulnerability reports — scope and rules of engagement matter
- CVEs (Common Vulnerabilities and Exposures): public database of known vulnerabilities
- Penetration testing methodology: scoped, time-limited, documented, reported

**What I did:**
Ran a basic nmap scan against a target machine — identified open ports and running services. Used a known vulnerability in an outdated service version to gain access. Reviewed the penetration testing engagement lifecycle from scoping to report delivery.

```bash
nmap -sV -sC -p- target.thm
nmap -A -T4 target.thm
```

**Takeaways:**
`-sV` (service version detection) and `-sC` (default scripts) are the nmap flags you run on every engagement. Identifying the service version is the bridge between "port is open" and "this is exploitable" — version → CVE → exploit.

---

### Room 31 — Become a Defender
🔗 https://tryhackme.com/room/becomeadefender

**Key Concepts:**
- SOC (Security Operations Centre): team responsible for monitoring, detecting, and responding to threats
- SIEM: aggregates logs from all sources, applies correlation rules to surface alerts
- Incident response phases: Preparation → Identification → Containment → Eradication → Recovery → Lessons Learned
- Threat intelligence: structured knowledge about adversaries — TTPs (Tactics, Techniques, Procedures)
- MITRE ATT&CK framework: comprehensive matrix of adversary techniques mapped to real-world threat groups
- IOCs (Indicators of Compromise): evidence of a breach — malicious IPs, file hashes, domain names
- Hardening: reducing the attack surface by disabling services, patching, configuring correctly

**What I did:**
Worked through a simulated incident response scenario — identified a compromised host via SIEM alerts, traced the attack timeline using logs, isolated the affected system, and documented findings. Mapped the attacker's behaviour to MITRE ATT&CK techniques.

**Takeaways:**
MITRE ATT&CK is the common language between offensive and defensive teams. As a pentester, documenting your techniques using ATT&CK IDs makes reports more useful for the blue team — they can directly map your findings to their detection coverage and identify gaps.

---

---

## Path-Level Reflection

### What I learned overall
The Pre Security path builds a solid mental model of how technology works — from electrons moving through hardware up to HTTP requests in a browser. It gives proper names and structure to concepts that are often learned informally. The sections on networking (OSI, TCP/IP, packets) and web fundamentals (DNS, HTTP, cookies) are the most directly applicable to real security work.

### What connected across sections
DNS (Section 3) sits on top of the networking concepts from Section 2 — it's just UDP packets going to port 53. HTTP (Section 3) sits on top of TCP (Section 2). SQL injection (hinted at in Section 6) only makes sense once you understand how web apps query databases. The CIA Triad in Section 7 is a framework that organises everything from the earlier sections — every attack covered throughout the path violates one of those three properties.


*Writeup by [gresium](https://tryhackme.com/p/gresium) | TryHackMe — Pre Security Path*
