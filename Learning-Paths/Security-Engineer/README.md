# Security Engineer

**Status:** ✅ Completed  
**Platform:** TryHackMe  
**Level:** Intermediate  

---

## Overview

The Security Engineer path covers the defensive architecture and engineering side of 
cybersecurity. Where penetration testing paths teach how to break systems, this path 
teaches how systems are designed to resist being broken — and more critically, 
where those designs fail.

For an offensive security practitioner, understanding how defences are built is not 
optional knowledge. Every bypass requires understanding what you are bypassing. 
Every misconfiguration you exploit in a real engagement exists because a security 
engineer made a decision — this path explains what those decisions look like 
and why they go wrong.

---

## Modules & Rooms

### 🔹 Introduction to Security Engineering
- What a security engineer does day-to-day versus a penetration tester or SOC analyst
- Security requirements gathering: translating business needs into security controls
- Security frameworks: ISO 27001, NIST CSF, and how organisations structure their 
  security programs
- Risk management: threat identification, likelihood/impact assessment, risk treatment

### 🔹 Threat Modelling
- **STRIDE** — Spoofing, Tampering, Repudiation, Information Disclosure, 
  Denial of Service, Elevation of Privilege — applied to real system architectures
- **Attack Trees** — Decomposing attacker goals into step-by-step attack paths
- **DREAD** — Damage, Reproducibility, Exploitability, Affected Users, Discoverability
- Practical: Building a threat model for a web application from scratch

### 🔹 Secure Network Architecture
- Defence-in-depth: layered security controls across network segments
- Network segmentation: VLANs, DMZs, and why flat networks are high-risk
- Firewall architecture: stateless vs stateful, next-generation firewalls, 
  rule ordering and common misconfigurations
- VPNs: IPSec vs SSL/TLS VPNs, split tunnelling, remote access security
- Zero Trust Architecture: "never trust, always verify" — how modern enterprises 
  are moving away from perimeter-based security
- Intrusion Detection/Prevention Systems (IDS/IPS): signature vs anomaly-based, 
  placement in network architecture, evasion techniques attackers use

### 🔹 Identity and Access Management
- Authentication protocols: LDAP, RADIUS, SAML, OAuth 2.0, OpenID Connect
- Single Sign-On (SSO) — how it works, how it fails, what happens when it's 
  misconfigured (a single point of compromise)
- Multi-Factor Authentication (MFA): TOTP, hardware tokens, push notifications, 
  and why SMS-based MFA is weak
- Privilege management: least privilege principle, just-in-time access, 
  Privileged Access Workstations (PAWs)
- Active Directory security: group policy, delegation, common misconfigurations 
  that enable lateral movement

### 🔹 Cryptography in Practice
- TLS handshake in detail: certificate verification, cipher suite negotiation, 
  session key derivation
- Certificate Authority (CA) infrastructure: root CAs, intermediate CAs, 
  certificate pinning, and certificate transparency logs
- Key management: key rotation, secure storage (HSMs), key escrow risks
- Common cryptographic failures: weak cipher suites, expired certificates, 
  improper certificate validation, padding oracle attacks

### 🔹 Software Security
- OWASP Top 10 from a defensive perspective — not just what the vulnerabilities are 
  but how developers should prevent them at the code level
- Input validation and output encoding: the correct way to handle untrusted data
- Secure coding principles: parameterised queries, prepared statements, 
  security headers (CSP, HSTS, X-Frame-Options)
- Dependency management: identifying vulnerable third-party libraries, 
  supply chain risks, SBOMs (Software Bill of Materials)
- Code review methodology: what to look for when reviewing code for security issues

### 🔹 Cloud Security
- AWS security fundamentals: IAM roles, policies, and the principle of least privilege 
  applied to cloud resources
- Common cloud misconfigurations: overly permissive S3 buckets, exposed metadata 
  endpoints, unrestricted security groups
- Cloud-native threats: credential theft from IMDS (Instance Metadata Service), 
  IAM privilege escalation, cross-account attacks
- Azure Active Directory: tenant misconfigurations, conditional access policies
- Container security: Docker security best practices, image hardening, 
  runtime security monitoring

### 🔹 SDLC & DevSecOps Integration
- Security in the Software Development Life Cycle: shifting security left
- Threat modelling during design phase
- SAST (Static Application Security Testing) integration into CI/CD pipelines
- Dependency scanning and software composition analysis (SCA)
- Security gates: what conditions should block a deployment
- Penetration testing as part of the release process

---

## Tools & Technologies Encountered
`Nessus` `Wireshark` `AWS IAM` `OpenSSL` `OWASP ZAP` `Burp Suite` `Snort` `Suricata`

---

## Key Takeaways

The most valuable insight from this path: defenders and attackers are looking at the 
same systems from opposite directions. A WAF rule that blocks a specific payload pattern 
immediately suggests that slightly modified payloads might bypass it. An MFA implementation 
that relies on SMS opens the door to SIM swapping. Understanding defensive architecture 
at this level directly improves the quality of offensive work — because you understand 
not just how to exploit something, but *why* that weakness exists.
