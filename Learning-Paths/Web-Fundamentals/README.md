# DevSecOps

**Status:** ✅ Completed  
**Platform:** TryHackMe  
**Level:** Intermediate → Advanced  

---

## Overview

DevSecOps explores one of the fastest-growing and most overlooked attack surfaces in 
modern cybersecurity: the software development and deployment pipeline itself. 
As organisations adopt CI/CD, containers, and infrastructure-as-code, the attack 
surface expands beyond the application — into the tools, pipelines, and automation 
that build and deploy it.

This path covers both how to secure these environments and, critically, how attackers 
target them. Pipeline exploitation, container escape, supply chain attacks, and 
secrets theft are not theoretical — they are techniques used in real-world breaches 
including some of the most significant attacks of recent years.

---

## Modules & Rooms

### 🔹 DevSecOps Introduction
- What DevSecOps means in practice: integrating security at every stage of development
- The "shift left" philosophy: finding vulnerabilities during development rather than 
  after deployment
- The DevSecOps toolchain: SAST, DAST, SCA, secrets scanning, container scanning
- Understanding the CI/CD pipeline as an attacker target: why pipeline compromise 
  can mean full infrastructure access

### 🔹 SAST — Static Application Security Testing
- How SAST tools analyse source code without executing it
- Identifying common vulnerability patterns: SQL injection sinks, XSS output points, 
  hardcoded credentials, insecure cryptography usage
- Tools: `Semgrep`, `Bandit` (Python), `ESLint` with security plugins
- Practical: Running SAST scans against intentionally vulnerable codebases and 
  triaging real vs false positive findings

### 🔹 SCA — Software Composition Analysis
- Dependency confusion attacks: how attackers publish malicious packages with 
  names that match internal private packages
- Identifying vulnerable third-party libraries: CVE databases, CVSS scoring 
  applied to dependencies
- Tools: `OWASP Dependency-Check`, `Snyk`, `npm audit`, `pip-audit`
- Supply chain attack methodology: SolarWinds, XZ Utils backdoor — 
  how build system compromise propagates to every downstream consumer

### 🔹 CI/CD Pipeline Security
- Jenkins exploitation: exposed Jenkins instances, script console abuse for RCE, 
  stored credentials theft, pipeline poisoning through pull request injection
- GitLab CI/CD: `.gitlab-ci.yml` manipulation, runner token theft, 
  environment variable leakage
- GitHub Actions: workflow injection via untrusted input, secret exfiltration, 
  compromised third-party actions
- Pipeline poisoning: how an attacker who can modify pipeline configuration 
  achieves code execution in trusted environments with elevated privileges
- Practical: Exploiting a misconfigured Jenkins pipeline for full RCE and 
  lateral movement

### 🔹 Secrets Management
- The most common mistake in development: hardcoded credentials in source code
- Git history secrets: credentials committed and removed but still visible 
  in version history — tools: `truffleHog`, `git-secrets`, `gitleaks`
- Environment variable security: `.env` files, exposure through error messages, 
  container environment inspection
- Proper secrets management: HashiCorp Vault, AWS Secrets Manager, 
  Azure Key Vault — and how each can be misconfigured

### 🔹 Container Security
- Docker security fundamentals: what namespaces and cgroups actually provide 
  (and what they do not)
- Container escape techniques: privileged container abuse, mounted Docker socket 
  exploitation, kernel vulnerability exploitation from within a container
- Image security: scanning for vulnerabilities in base images, 
  minimising attack surface with distroless images, image signing
- Docker Compose security misconfigurations: exposed ports, volume mounts, 
  privileged flags left in production configs

### 🔹 Kubernetes Security
- Kubernetes architecture security: API server exposure, etcd encryption, 
  node-to-node communication
- RBAC misconfigurations: overly permissive ClusterRoles, wildcard permissions, 
  service account token abuse
- Pod security: privileged pods, hostPath mounts, running as root
- Kubernetes secrets: why base64 encoding is not encryption, 
  proper secrets management with sealed secrets or external vaults
- Practical: Exploiting a misconfigured Kubernetes cluster from initial pod 
  compromise to cluster-admin via RBAC abuse

### 🔹 Infrastructure as Code Security
- Terraform security: state file exposure (contains plaintext secrets), 
  overly permissive IAM policies created by IaC, remote code execution 
  via malicious Terraform providers
- Ansible security: playbook injection, vault secret management, 
  insecure inventory configurations
- Practical: Identifying and exploiting misconfigurations in Terraform 
  and Ansible configurations

---

## Tools & Technologies Mastered
`Jenkins` `GitLab CI` `GitHub Actions` `Docker` `Kubernetes` `kubectl` `Trivy` 
`Semgrep` `truffleHog` `gitleaks` `HashiCorp Vault` `Terraform` `Ansible` 
`OWASP Dependency-Check` `Snyk`

---

## Key Takeaways

Modern infrastructure is complex, interconnected, and largely automated — which means 
a single misconfiguration in a pipeline can cascade into full infrastructure compromise. 
This path made clear that CI/CD pipelines are high-value targets precisely because they 
operate with elevated trust by design. The supply chain attack angle is particularly 
relevant: as more organisations consume open-source dependencies without proper vetting, 
the attack surface expands upstream of the application itself. DevSecOps is not just 
a defensive skill — it is essential offensive knowledge for any penetration tester 
working against modern cloud-native environments.
