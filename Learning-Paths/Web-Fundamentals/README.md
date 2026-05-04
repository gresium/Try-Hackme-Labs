# Web Fundamentals

**Status:** ✅ Completed  
**Platform:** TryHackMe  
**Level:** Beginner → Intermediate  

---

## Overview

The Web Fundamentals path is the essential prerequisite for any serious web application 
security work. It builds a complete technical understanding of how the web operates — 
from the HTTP request/response cycle through to the mechanics of the most common 
vulnerability classes — and introduces the tooling that professional web testers 
use on every engagement.

The key insight this path delivers: every web vulnerability exists because a developer 
made an assumption about user input that turned out to be wrong. Understanding exactly 
where those assumptions are made — and how to violate them — is the core skill this 
path develops.

---

## Modules & Rooms

### 🔹 How the Web Works
- **DNS in Detail** — The full DNS resolution chain: recursive resolvers, 
  root nameservers, TLD nameservers, authoritative nameservers. Record types: 
  A, AAAA, CNAME, MX, TXT, NS. DNS caching, TTL, and why stale DNS records 
  cause reachability issues in security testing environments
- **HTTP in Detail** — HTTP/1.1 vs HTTP/2, all HTTP methods and their intended use 
  vs how they are abused (PUT for file upload, DELETE for data destruction), 
  status code classes and what each reveals about server behaviour, 
  request and response headers — security-relevant ones: `Set-Cookie`, 
  `X-Forwarded-For`, `Authorization`, `Content-Type`, `Location`
- **How Websites Work** — Server-side rendering vs client-side rendering, 
  how JavaScript interacts with the DOM, how databases sit behind web applications, 
  and why sensitive data ends up in HTML source
- **Putting It All Together** — The complete lifecycle of a web request: 
  DNS resolution → TCP connection → TLS handshake → HTTP request → 
  server processing → database query → response rendering. 
  Where CDNs, load balancers, and WAFs sit in this chain

### 🔹 Introduction to Web Hacking
- **Walking an Application** — Manual application review using only a browser: 
  page source inspection, DevTools (Network tab, Console, Storage), 
  finding hidden forms, comments with sensitive information, 
  and JavaScript files with hardcoded values
- **Content Discovery** — The three methods: manual (robots.txt, sitemap.xml, 
  HTTP headers, favicon hash fingerprinting), automated (Gobuster, FFuF wordlist fuzzing), 
  and OSINT (Google dorking, Wayback Machine, GitHub code search)
- **Subdomain Enumeration** — Brute force, DNS zone transfers (AXFR), 
  certificate transparency log searching (crt.sh), and virtual host enumeration 
  for targets with multiple sites on one IP
- **Authentication Bypass** — Username enumeration via response time differences 
  or distinct error messages, brute forcing with Hydra, logic flaw exploitation 
  (bypassing authentication checks entirely through parameter manipulation), 
  cookie tampering
- **IDOR** — Horizontal privilege escalation (accessing other users' data), 
  vertical privilege escalation (accessing admin functions), 
  IDOR in encoded parameters (base64, hashed IDs), 
  IDOR in API endpoints and unpredictable IDs
- **File Inclusion (LFI/RFI)** — Path traversal (`../../../etc/passwd`), 
  null byte injection, PHP wrappers (`php://filter/convert.base64-encode/resource=`), 
  log poisoning for remote code execution via LFI, 
  Remote File Inclusion when `allow_url_include` is enabled
- **Intro to SSRF** — Basic SSRF to access internal services, 
  blind SSRF via out-of-band detection, bypassing SSRF filters (URL encoding, 
  IP format variations, DNS rebinding concepts), 
  accessing cloud metadata endpoints (AWS `169.254.169.254`)
- **XSS** — Reflected XSS (user input reflected immediately in response), 
  Stored XSS (payload persisted in database, executed on every load), 
  DOM-based XSS (JavaScript reads from URL and writes to DOM without server involvement), 
  cookie stealing payloads, keylogger injection, BeEF framework introduction
- **Command Injection** — In-band (output visible directly), 
  blind (time-based and out-of-band via DNS/HTTP callbacks), 
  common injection points, filter bypass with encoding and alternative syntax
- **SQL Injection** — Classic in-band: UNION-based column enumeration and data extraction, 
  Error-based for database fingerprinting. Blind: Boolean-based (different responses 
  for true/false conditions) and time-based (`SLEEP()`/`WAITFOR DELAY`). 
  Manual exploitation workflow and `sqlmap` for automation

### 🔹 Burp Suite
- **The Basics** — Browser proxy configuration, FoxyProxy, intercepting and forwarding 
  requests, the HTTP history log, scope configuration
- **Repeater** — The core Burp workflow: intercept → send to Repeater → 
  modify → analyse → repeat. Why manual testing with Repeater finds what 
  scanners miss
- **Intruder** — Attack types: Sniper (single position), 
  Battering Ram (same payload in all positions), Pitchfork (parallel payloads), 
  Cluster Bomb (all combinations). Payload types: wordlists, numbers, 
  character fuzz strings. Rate limiting and Burp Community restrictions
- **Other Modules** — Decoder for URL/Base64/hex encoding, 
  Comparer for diffing two responses, Sequencer for token randomness analysis

---

## Tools Mastered
`Burp Suite` `Gobuster` `FFuF` `sqlmap` `Hydra` `Netcat` `curl` 
`browser DevTools` `crt.sh` `Shodan`

---

## Key Takeaways

The web is the primary attack surface for almost every real-world penetration test. 
This path built the mental model that drives effective web application security work: 
every input field is a potential injection point, every response leaks information about 
the server, and every assumption a developer makes about what a user will send 
is a potential vulnerability. Burp Suite went from a tool to a natural extension of 
the testing workflow. LFI log poisoning and SSRF to cloud metadata were particular 
highlights — both techniques appear frequently in real CTFs and red team engagements.
