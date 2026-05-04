# Cyber Security 101

**Status:** ✅ Completed  
**Certification:** SEC1 — TryHackMe Professional Certification  
**Platform:** TryHackMe  
**Level:** Beginner → Intermediate  

---

## Overview

Cyber Security 101 is TryHackMe's structured bridge between foundational knowledge and 
real-world security practice. It takes everything the Pre Security path introduced and 
puts it into a security context — shifting the mindset from "how does this work" to 
"how does this get attacked and defended."

The path covers both offensive and defensive fundamentals, giving a complete picture of 
the cybersecurity field before specialising. It is broader in scope than any single 
career track path, deliberately designed to show students the full landscape so they can 
make informed decisions about where to specialise next.

Completing this path earns the SEC1 Professional Certification from TryHackMe.

---

## Modules & Rooms

### 🔹 Start Your Cyber Journey
Introduction to the TryHackMe platform, cybersecurity careers, and what a day in the 
life of various security roles looks like — from SOC analyst to penetration tester. 
Covers the difference between offensive (red team) and defensive (blue team) security, 
and the legal and ethical framework that governs security work.

### 🔹 Networking
Building on Pre Security fundamentals with a security lens:
- Packet analysis and what attackers look for in traffic captures
- Network protocols and their inherent weaknesses (Telnet, FTP, HTTP — all plaintext)
- How network-level attacks like ARP poisoning and MITM work conceptually
- Wireshark introduction for capturing and reading live traffic

### 🔹 How the Web Works (Security Context)
- HTTP/HTTPS from an attacker's perspective — what information leaks in headers
- Cookies, sessions, and how authentication state is managed (and broken)
- Introduction to web application vulnerabilities at a conceptual level

### 🔹 Linux & Windows (Security-Focused)
- Linux: Users, permissions, SUID/GUID bits, common misconfigurations
- Windows: Active Directory introduction, common attack surfaces on Windows systems
- Log locations on both systems — where defenders look, where attackers try to cover tracks

### 🔹 Cryptography
- Symmetric vs asymmetric encryption
- Common algorithms: AES, RSA, and their practical applications
- Hashing: MD5, SHA1, SHA256 — why hashes are used for passwords and integrity checks
- PKI and how TLS certificates establish trust in HTTPS connections
- Practical: Cracking weak hashes using wordlists

### 🔹 Offensive Security Fundamentals
- What a penetration test actually looks like — scope, rules of engagement, methodology
- Introduction to reconnaissance: passive (OSINT, Shodan, whois) and active (port scanning)
- First exposure to Metasploit: modules, exploits, payloads, sessions
- Vulnerability scanning with Nessus and OpenVAS concepts

### 🔹 Defensive Security Fundamentals
- Security Operations Centre (SOC) structure and analyst workflow
- Introduction to SIEM: log aggregation, alerting, and incident triage
- Threat intelligence: indicators of compromise (IoCs), MITRE ATT&CK framework overview
- Digital forensics basics: disk images, memory dumps, timeline analysis
- Incident response lifecycle: Preparation → Identification → Containment → Eradication 
  → Recovery → Lessons Learned

---

## Tools & Technologies Encountered
`Wireshark` `Metasploit` `Nmap` `Gobuster` `John the Ripper` `hashcat` `Shodan` 
`MITRE ATT&CK Navigator`

---

## Certification

Upon completing this path, a **SEC1 Professional Certification** was awarded by TryHackMe, 
validating foundational knowledge across networking, cryptography, web security, 
offensive techniques, and defensive security operations.

---

## Key Takeaways

This path confirmed offensive security as the right direction. Seeing how defenders think, 
what logs they check, and what alerts they set gave direct insight into what attackers need 
to evade. Understanding the full picture — both red and blue — makes for a more effective 
and well-rounded penetration tester. The MITRE ATT&CK framework introduced here became 
a recurring reference point in every path that followed.
