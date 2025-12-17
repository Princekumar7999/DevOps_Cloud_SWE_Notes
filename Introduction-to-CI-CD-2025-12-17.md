# Introduction to CI/CD

**Class date:** Wednesday, 17 December 2025  
**Topic:** Continuous Integration and Continuous Deployment — concepts, pipeline stages, and best practices  
**Note:** Class notes were not available; this document is based on the topic and standard CI/CD practice.

---

## 1. What Is CI/CD?

**CI/CD** stands for **Continuous Integration** and **Continuous Deployment** (or **Continuous Delivery**). It is the practice of **automating** the build, test, and deployment of software so that changes are integrated frequently, tested early, and released reliably.

### 1.1 Continuous Integration (CI)

- **Definition:** Automatically **building** and **testing** code whenever it is committed or merged into a shared repository.
- **Goals:** Catch integration and quality issues early, keep the main branch in a **deployable state**, and give developers **fast feedback** (ideally within minutes).
- **Typical activities:** Code checkout, dependency install, **lint**, **compile/build**, **unit tests**, and often **security scans** (SAST, SCA). Output is usually an **artifact** (e.g. JAR, Docker image).

### 1.2 Continuous Deployment / Delivery (CD)

- **Continuous Delivery:** Code is always in a state that **can** be released; deployment to production may still be **manual** or gated.
- **Continuous Deployment:** Every change that passes the pipeline is **automatically** deployed to production (or to the next environment) with no manual approval step.
- **Goals:** Reduce deployment risk, enable **frequent releases**, and make rollbacks easier by deploying small, incremental changes.

---

## 2. Pipeline Stages (Typical Flow)

| Stage | Purpose |
|-------|---------|
| **1. Source / version control** | Code lives in a repo (e.g. Git). Triggers (push, PR) start the pipeline. Branching strategies (e.g. GitFlow) support parallel work. |
| **2. Build** | Compile and package the application (e.g. Maven `package`, `docker build`). Produce a single, versioned **artifact**. |
| **3. Test** | Run **unit**, **integration**, and optionally **e2e** tests. Fail the pipeline if tests fail so broken code does not progress. |
| **4. Security & quality** | **Linting**, **SAST** (code), **SCA** (dependencies), and optionally **container scanning**. Shift-left security. |
| **5. Artifact storage** | Publish the artifact to a **repository** (e.g. Nexus, Docker Hub, ECR) so the same build can be promoted across environments. |
| **6. Deploy** | Deploy the artifact to **staging** (and then **production**). Can be manual, automatic, or gated by approvals. |

Stages are often implemented as **jobs** or **steps** in a pipeline tool (e.g. GitHub Actions, Jenkins, GitLab CI).

---

## 3. Why CI/CD Matters

- **Faster feedback:** Issues are found in minutes instead of at release time.
- **Lower risk:** Small, frequent changes are easier to reason about and roll back.
- **Consistency:** Same process every time — no “works on my machine” or manual mistakes.
- **Faster time to market:** Automation shortens the path from commit to production.

---

## 4. Best Practices

- **Commit often:** Small, frequent commits reduce merge conflicts and integration surprises.
- **Automate everything:** Build, test, and deploy via pipeline; avoid manual steps for routine releases.
- **Fail fast:** If a stage fails, stop the pipeline and block promotion until it’s fixed.
- **Use version control for pipeline config:** Store pipeline definitions (YAML, Jenkinsfile) in Git for audit and rollback.
- **Security in the pipeline:** Integrate SAST, SCA, and container scanning so only scanned artifacts are promoted.
- **Keep pipelines fast:** Optimize so developers get feedback quickly (e.g. under 10–15 minutes for CI).
- **Separate CI and CD when needed:** CI produces and tests artifacts; CD consumes them and deploys. Clear separation helps with ownership and security.

---

## 5. Common Tools

- **CI/CD platforms:** GitHub Actions, GitLab CI/CD, Jenkins, Azure DevOps Pipelines, CircleCI.
- **Build:** Maven, Gradle, npm, Docker.
- **Testing:** JUnit, pytest, Selenium; test runners in the pipeline.
- **Security:** CodeQL (SAST), OWASP Dependency-Check (SCA), Trivy (container scan).
- **Artifact stores:** Docker Hub, Nexus, Artifactory, AWS ECR, GCR.

---

## 6. Summary

- **CI** = automate build and test on every change; keep mainline deployable.
- **CD** = automate (or streamline) deployment so releases are frequent and low-risk.
- A **pipeline** runs in **stages**: source → build → test → security → artifact → deploy.
- **Best practices:** automate, fail fast, use version-controlled pipelines, and integrate security (DevSecOps).
