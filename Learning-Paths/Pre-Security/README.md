# Pre Security

**Status:** ✅ Completed  
**Platform:** TryHackMe  
**Level:** Beginner  
**Estimated Hours:** 40+ hours  

---

## Overview

The Pre Security path is TryHackMe's ground-zero starting point for anyone entering cybersecurity. 
Before you can attack or defend any system, you need to understand how that system actually works — 
this path builds exactly that foundation. Covering everything from how computers function internally 
to how data moves across networks and how operating systems manage resources, it is the bedrock that 
every advanced concept eventually traces back to.

This is not a path you rush through. The concepts introduced here — subnetting, the OSI model, 
DNS resolution, file permissions, process management — come back constantly in every offensive and 
defensive room that follows.

---

## Modules & Rooms

### 🔹 Cyber Security Introduction
An overview of the cybersecurity landscape, introducing the two pillars of the field: 
web application security and network security. Includes an interactive lab where you 
simulate hacking a fictional social media account — a first taste of what thinking like 
an attacker actually looks like in practice.

### 🔹 Network Fundamentals
The most technically dense module in the path. Covers:
- **What is Networking?** — IP addresses, MAC addresses, how devices identify each other
- **Intro to LAN** — LAN topologies (star, bus, ring), subnetting, ARP and DHCP protocols
- **OSI Model** — All 7 layers in depth: Physical, Data Link, Network, Transport, Session, 
  Presentation, Application. Includes an interactive OSI game to reinforce layer logic
- **Packets & Frames** — TCP/IP three-way handshake, UDP, port numbers, how data is 
  segmented and reassembled across a network
- **Extending Your Network** — Port forwarding, firewalls, VPNs, and network extension 
  technologies used in enterprise environments

### 🔹 How the Web Works
Critical foundation for all web application security work:
- **DNS in Detail** — Domain hierarchy, record types (A, CNAME, MX, TXT), how a browser 
  resolves a domain to an IP, TTL and caching behaviour
- **HTTP in Detail** — HTTP methods (GET, POST, PUT, DELETE), status codes, headers, 
  cookies, and how HTTPS wraps HTTP with TLS encryption
- **How Websites Work** — Front-end HTML/JavaScript, back-end servers, databases, 
  sensitive data exposure, and HTML injection basics
- **Putting It All Together** — Load balancers, CDNs, WAFs, static vs dynamic content, 
  full request lifecycle from browser to server and back

### 🔹 Linux Fundamentals (Parts 1–3)
- Part 1: First commands (`echo`, `whoami`, `ls`, `cd`, `cat`, `find`, `grep`), 
  filesystem navigation, shell operators for chaining and redirecting output
- Part 2: SSH remote access, command flags and manual pages (`man`), file permissions 
  (`chmod`, `chown`), users and groups, common directories (`/etc`, `/var`, `/tmp`)
- Part 3: Text editors (`nano`, `vim`), process management (`ps`, `top`, `kill`, `fg`, `bg`), 
  package management (`apt`), Python web servers, automation with cron jobs

### 🔹 Windows Fundamentals (Parts 1–3)
- Part 1: NTFS filesystem, file/folder permissions, special permissions, 
  `icacls` command, alternate data streams
- Part 2: System Configuration (`msconfig`), UAC settings, Computer Management tools 
  (Task Scheduler, Shared Folders), System Information (`MSInfo32.exe`), 
  Resource Monitor, Registry Editor basics
- Part 3: Windows Update, Windows Security center, BitLocker, Volume Shadow Copy Service, 
  Windows Firewall, and key defensive utilities

---

## Tools & Technologies Encountered
`SSH` `ping` `traceroute` `nslookup` `dig` `Netcat` `Python HTTP server` `Windows Registry Editor`

---

## Key Takeaways

Every room that comes after this path traces back to something learned here. Understanding 
the TCP handshake explains why port scans work. Understanding DNS explains why subdomain 
enumeration is possible. Understanding Linux file permissions explains every privilege 
escalation technique on a Linux system. This path is not optional preparation — it is the 
mental model that makes everything else click.
