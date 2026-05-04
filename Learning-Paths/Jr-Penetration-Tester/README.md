# Jr Penetration Tester 

**Status:** ✅ Completed  
**Platform:** TryHackMe  
**Level:** Intermediate  
**Rooms:** 38 rooms across 8 sections  

---

## Overview

The Jr Penetration Tester path is where theory ends and hands-on offensive security begins. 
It follows a complete penetration testing methodology from initial reconnaissance through 
to post-exploitation and reporting — mirroring how real engagements are structured in 
professional environments.

This is one of TryHackMe's flagship offensive security paths. Every major attack category 
is covered not through reading alone, but through actively exploiting intentionally 
vulnerable machines. By the end, the workflow of approaching an unknown target, identifying 
attack vectors, gaining initial access, escalating privileges, and maintaining access 
becomes second nature.

---

## Sections & Rooms

### 🔹 Section 1 — Pentesting Fundamentals
- **Pentesting Fundamentals** — Ethics, legal scope, rules of engagement, types of 
  penetration tests (black/grey/white box), and the stages of an engagement
- **Principles of Security** — CIA Triad (Confidentiality, Integrity, Availability), 
  Privilege Identity Management (PIM), Privileged Access Management (PAM), 
  Bell-La Padula and Biba security models

### 🔹 Section 2 — Introduction to Web Hacking
Ten rooms covering the most critical web vulnerabilities:
- **Walking an Application** — Manual inspection using browser DevTools, 
  finding hidden content without any tools
- **Content Discovery** — Manual discovery, automation with Gobuster/FFuF, 
  OSINT methods, HTTP headers, `robots.txt`, sitemaps
- **Subdomain Enumeration** — DNS brute forcing, certificate transparency logs, 
  virtual host enumeration
- **Authentication Bypass** — Brute force attacks, logic flaws, cookie manipulation, 
  username enumeration via response differences
- **IDOR** — Horizontal and vertical privilege escalation through insecure object 
  references in URLs, API parameters, and encoded values
- **File Inclusion (LFI/RFI)** — Path traversal, log poisoning for RCE, 
  PHP wrapper abuse (`php://filter`, `data://`)
- **SSRF** — Basic and blind SSRF, bypassing filters, accessing internal services 
  and cloud metadata endpoints
- **XSS** — Reflected, stored, and DOM-based XSS, payload construction, 
  cookie stealing, keylogging via script injection
- **Command Injection** — In-band and blind command injection, filter bypass techniques
- **SQL Injection** — In-band (union-based), blind (boolean and time-based), 
  manual exploitation and `sqlmap` automation

### 🔹 Section 3 — Burp Suite (5 rooms)
Deep dive into the industry-standard web proxy:
- **The Basics** — Proxy setup, intercepting and modifying requests, FoxyProxy
- **Repeater** — Manually crafting and replaying HTTP requests for vulnerability testing
- **Intruder** — Automated fuzzing, brute force attacks, payload positions and types
- **Other Modules** — Decoder, Comparer, Sequencer for analysing randomness in tokens
- **Extensions** — BApp Store, useful extensions for professional engagements

### 🔹 Section 4 — Network Security (9 rooms)
- **Passive Reconnaissance** — `whois`, `nslookup`, `dig`, DNSDumpster, Shodan.io
- **Active Reconnaissance** — `ping`, `traceroute`, `telnet`, `Netcat`
- **Nmap** — Live host discovery, basic and advanced port scans (SYN, UDP, FIN, 
  XMAS, NULL), service/version detection, OS fingerprinting, Nmap Scripting Engine (NSE)
- **Protocols & Servers** — FTP, SMTP, POP3, IMAP, SSH, Telnet — understanding 
  protocol weaknesses and what information they leak
- **Net Sec Challenge** — Practical room applying all network security techniques

### 🔹 Section 5 — Vulnerability Research
- **Vulnerability 101** — CVEs, CVSS scoring, exploit databases, responsible disclosure
- **Exploit Vulnerabilities** — Using ExploitDB and Searchsploit, identifying applicable 
  exploits for specific software versions
- **Vulnerability Capstone** — Full practical applying the research methodology

### 🔹 Section 6 — Metasploit (5 rooms)
Complete coverage of the Metasploit Framework:
- **Introduction** — MSFconsole, modules (auxiliary, exploit, payload, post), 
  workspace management
- **Exploitation** — Running exploits, session management, backgrounding shells
- **Meterpreter** — File system interaction, privilege escalation, pivoting, 
  persistence, Meterpreter commands
- **Working with Metasploit** — Module search, payload types (staged vs stageless), 
  `msfvenom` for custom payloads

### 🔹 Section 7 — Privilege Escalation
- **Linux PrivEsc** — Kernel exploits, SUID/GUID abuse, `sudo` misconfigurations, 
  PATH hijacking, cron job exploitation, writable `/etc/passwd`, NFS root squashing
- **Windows PrivEsc** — Service misconfigurations, DLL hijacking, AlwaysInstallElevated, 
  credential harvesting (PowerShell history, registry, configuration files), 
  token impersonation with `Meterpreter`

### 🔹 Section 8 — Capstone CTF Challenges
Three practical rooms that simulate real-world penetration tests from start to finish — 
no hints, full methodology required. These rooms test the ability to enumerate, exploit, 
escalate, and document findings independently.

---

## Tools & Technologies Mastered
`Nmap` `Burp Suite` `Metasploit/Meterpreter` `msfvenom` `sqlmap` `Gobuster` `FFuF` 
`Hydra` `Searchsploit/ExploitDB` `Netcat` `Nikto` `LinPEAS` `WinPEAS` `John the Ripper`

---

## Key Takeaways

This path is the foundation for OSCP preparation. The methodology it instills — 
enumerate everything, validate manually before automating, chain vulnerabilities, 
document as you go — is exactly how professional engagements run. 
Privilege escalation on both Linux and Windows became a particular strength, 
as did web application exploitation. The Burp Suite coverage is genuinely deep 
and transfers directly to real-world web app testing.
