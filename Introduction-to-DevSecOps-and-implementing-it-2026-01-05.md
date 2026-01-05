# Introduction to DevSecOps and Implementing It

**Class date:** Monday, 5 January 2026  
**Topic:** DevSecOps CI pipeline with GitHub Actions — build, test, SAST, SCA, Docker, Trivy, and push to Docker Hub

---

## 1. What We Are Building

A **production-style Continuous Integration** pipeline that:

- Pulls source code  
- Builds a Java application  
- Runs **linting** and **unit tests**  
- Performs **code security scanning (SAST)**  
- Performs **dependency scanning (SCA)**  
- Builds a **Docker image**  
- **Scans the image** for vulnerabilities  
- **Runs the container** and tests it (smoke test)  
- **Pushes** a verified image to **Docker Hub**  

Security is applied **before** the software is shipped — **DevSecOps** and **shift-left** in practice.

---

## 2. CI Architecture Overview

```
Developer Push → GitHub Actions Runner →
  Checkout → Build → Test → Security Scan →
  Docker Build → Image Scan → Container Test → Push to Registry
```

---

## 3. Prerequisites

- **Application:** Java Maven project, working **Dockerfile**, app listening on a known port (e.g. **8080**).  
- **Docker Hub:** Account, **username**, and **access token**.  
- **GitHub Secrets:** In **Settings → Secrets and variables → Actions**, add:
  - **DOCKERHUB_USERNAME** — your Docker Hub username  
  - **DOCKERHUB_TOKEN** — Docker Hub access token  

Secrets are used for **secure authentication**; never hardcode credentials.

---

## 4. Pipeline Trigger

```yaml
on:
  push:
    branches:
      - master
  workflow_dispatch:
```

- Runs on every **push to master**.  
- **workflow_dispatch** allows **manual** run from the Actions tab.

---

## 5. Job and Permissions

- **runs-on: ubuntu-latest** — pipeline runs on a GitHub-hosted Linux VM.  
- **permissions:**  
  - **contents: read** — read repo (checkout).  
  - **security-events: write** — upload scan results (e.g. SARIF) to **GitHub Security → Code scanning alerts**.

---

## 6. Pipeline Stages Explained

| # | Stage | Purpose |
|---|--------|---------|
| 1 | **Checkout** | `actions/checkout@v4` — get source. Without it, nothing to build. |
| 2 | **Setup Java** | `actions/setup-java@v3` — Java 11, Maven, dependency caching. |
| 3 | **Linting** | `mvn checkstyle:check` — coding standards. **continue-on-error: true** keeps pipeline running but surfaces violations (manage technical debt). |
| 4 | **SAST – CodeQL** | `github/codeql-action` — finds SQL injection, command injection, insecure deserialization, OWASP Top 10 in **source code**. Prevents insecure code from reaching production. |
| 5 | **SCA – OWASP Dependency Check** | Scans **Maven dependencies** for known CVEs. Protects against **supply chain** vulnerabilities. |
| 6 | **Unit tests** | `mvn test` — validate logic. Pipeline **fails** if tests fail; blocks broken builds. |
| 7 | **Build app** | `mvn clean package -DskipTests` — produce JAR/WAR for Docker. |
| 8 | **Docker build** | `docker build -t username/test:latest .` — create **immutable** image. |
| 9 | **Trivy image scan** | `aquasecurity/trivy-action` — scan image for OS and app **CVEs**. **exit-code: 1** fails pipeline on critical/high. Upload results (e.g. SARIF) to GitHub Security. |
| 10 | **Container runtime test** | `docker run ...` then e.g. `curl http://localhost:8080` — **smoke test**: container starts and app responds. |
| 11 | **Docker Hub login** | `docker/login-action@v3` using **DOCKERHUB_USERNAME** and **DOCKERHUB_TOKEN**. |
| 12 | **Push image** | `docker push username/test:latest` — publish only after all checks pass. Enables **CD** (deploy from this image). |

---

## 7. What Happens on Push

1. Developer pushes code.  
2. GitHub Actions starts a VM.  
3. Code is built and tested.  
4. Security scans run (SAST, SCA, Trivy).  
5. Docker image is built and scanned.  
6. Container is run and smoke-tested.  
7. If all pass → image is pushed to Docker Hub.

---

## 8. DevOps & Security Concepts

- **Continuous Integration** — automated build, test, and artifact creation.  
- **Shift-left security** — security in the pipeline, not only at the end.  
- **DevSecOps** — security integrated into dev and ops workflows.  
- **Supply chain protection** — SCA and image scanning for dependencies and base layers.  
- **Immutable artifacts** — one built image promoted through environments.  
- **Quality gates** — pipeline fails on test or critical/high vulnerability.  
- **Zero-trust credentials** — secrets in GitHub Secrets, not in code.

---

## 9. What Makes This “Industry-Grade”

- Multiple security layers: **code** (SAST), **dependencies** (SCA), **container** (Trivy).  
- Pipeline **fails** on critical/high vulnerabilities.  
- **Runtime verification** (container smoke test).  
- **Artifact promotion** only after checks.  
- **Secrets** handled via GitHub Secrets.  
- **GitHub Security** integration for centralized alerts.

---

## 10. Possible Extensions

- SonarQube (or similar) for quality gates.  
- **SBOM** (Software Bill of Materials) generation.  
- Push to **AWS ECR** (or other registries).  
- **CD** to EKS/ECS or Kubernetes.  
- **Slack** (or other) notifications.  
- **Artifact versioning** (tags from Git SHA or version).  
- Separate **infrastructure** pipeline (e.g. Terraform).

---

## 11. Summary

This pipeline is a **DevSecOps quality gate**: only code that is **built**, **tested**, **scanned** (code, dependencies, image), and **runtime-verified** is allowed to be **pushed** to the registry and used for deployment.