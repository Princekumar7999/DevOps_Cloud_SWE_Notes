# DevOps Introduction

**Class date:** Wednesday, 29 October 2025  
**Topic:** Overview of DevOps, Security Testing, CI/CD, and Microservices Communication

---

## 1. Overview of DevOps

### 1.1 What Is DevOps?

**DevOps** is a combination of three elements that work together to deliver applications and services **faster and more reliably**:

| Component | Description |
|-----------|-------------|
| **Practices** | Technical workflows such as CI/CD, Infrastructure as Code (IaC), automated testing, and monitoring. |
| **Cultural philosophies** | Mindset shifts: shared ownership, collaboration between Dev and Ops, blameless postmortems, and continuous improvement. |
| **Tools** | Technology that supports automation and collaboration (e.g., Jenkins, GitLab CI, Docker, Kubernetes, Terraform). |

DevOps **integrates software development (Dev) and IT operations (Ops)** so that building, testing, and running software happen in a single, streamlined flow. It is not only a set of tools—it is first a **cultural and organizational philosophy** that breaks down silos and encourages teams to own the full lifecycle of the product.

### 1.2 Why DevOps Exists

- **Traditional model:** Development writes code; Operations runs it. Handoffs are slow, feedback is late, and failures are often blamed on “the other team.”
- **DevOps model:** Dev and Ops (and often Security—DevSecOps) work together with shared goals, automated pipelines, and fast feedback. Small changes are integrated and deployed frequently, with quality and stability built in.

### 1.3 Challenges in DevOps

Adopting DevOps is not just a technical change:

- **Change management:** Teams with long-established processes may resist new workflows. Convincing people to adopt new methodologies and tools is one of the **hardest parts** of DevOps.
- **Culture shift:** Moving from “throw over the wall” to “we own it together” requires trust, transparency, and leadership support.
- **Skill and tool adoption:** Teams need to learn automation, CI/CD, and often cloud/container technologies.

Addressing these cultural and organizational aspects is as important as introducing the right practices and tools.

---

## 2. Key Concepts in DevOps

### 2.1 DevOps Philosophy

The **DevOps philosophy** is the application of principles that **unify software development (Dev) and software operation (Ops)**. Core ideas include:

- **Shared ownership** — Everyone is responsible for delivery and reliability, not just “dev” or “ops.”
- **Continuous improvement** — Small, iterative changes and a blameless culture for learning from incidents.
- **Early and frequent feedback** — From automated tests, monitoring, and users, so issues are found and fixed quickly.
- **Automation** — Reduce manual, error-prone work so humans can focus on design and improvement.

### 2.2 Shift Left Approach

**Shift Left** means moving **testing (and security) earlier** in the development process so that issues are **detected and fixed sooner**, when they are cheaper and easier to address.

#### Traditional vs Shift Left

- **Traditional:** Testing and security often happen late—right before or after release. Bugs and vulnerabilities are expensive to fix at that stage.
- **Shift Left:** Testing and security are integrated into **design, coding, and build** phases. Developers get fast feedback in the IDE or in the CI pipeline.

#### Benefits of Shift Left

| Benefit | Explanation |
|--------|-------------|
| **Lower cost** | Fixing a bug or vulnerability in code is far cheaper than fixing it in production. |
| **Faster releases** | Fewer last-minute surprises and security bottlenecks. |
| **Better security posture** | Vulnerabilities are found and remediated earlier. |
| **Shared responsibility** | Security becomes part of development (aligns with DevSecOps). |

#### How to Implement Shift Left

- Define **security and quality requirements** in planning and design.
- **Automate** security and quality checks in the CI/CD pipeline (e.g., SAST, SCA, unit/integration tests).
- Provide **developer security training** and **security champions** in teams.
- Use **SAST** and other static checks during coding; run **SCA** on dependencies as part of the build.

Shift Left fits naturally with **Agile** and **DevOps**: small increments, frequent integration, and continuous feedback.

---

## 3. Security Testing in DevOps

Security testing in DevOps is **automated and integrated** into the pipeline. Three main types are commonly used together.

### 3.1 Static Application Security Testing (SAST)

**SAST** analyzes **source code** (and sometimes bytecode) **without executing** the application.

| Aspect | Detail |
|--------|--------|
| **Type** | “White box” — has access to source code. |
| **When** | During development and in CI, before or at build time. |
| **Finds** | Code-level issues: OWASP Top 10 (e.g., SQL injection, XSS), hardcoded secrets, unsafe APIs, duplicate or risky code patterns. |
| **Limitation** | Cannot find runtime or environment-specific issues; may produce false positives. |

**Example tools:** Semgrep, CodeQL, Bandit, SonarQube, Checkmarx, Veracode, Synopsys Coverity.

### 3.2 Dynamic Application Security Testing (DAST)

**DAST** analyzes the application **while it is running**, from the outside, like an attacker would.

| Aspect | Detail |
|--------|--------|
| **Type** | “Black box” — no source code required. |
| **When** | After the app is deployed (e.g., staging); often in CD or a dedicated security stage. |
| **Finds** | Runtime vulnerabilities, misconfigurations, and issues that only appear in a live environment. |
| **Limitation** | Runs later in the SDLC; may not pinpoint exact line of code. |

**Example tools:** OWASP ZAP, Burp Suite, Acunetix.

### 3.3 Software Composition Analysis (SCA)

**SCA** scans **open-source and third-party** dependencies (libraries, frameworks) for **known vulnerabilities**.

| Aspect | Detail |
|--------|--------|
| **Focus** | Dependencies listed in package manifests (e.g., `package.json`, `pom.xml`, `requirements.txt`). |
| **Finds** | Known CVEs in libraries; license risks; outdated or unmaintained packages. |
| **Why it matters** | A large share of modern applications use open source; one vulnerable library can compromise the whole system (supply chain risk). |

**Example tools:** Snyk, Dependabot, WhiteSource, Black Duck, OWASP Dependency-Check.

### 3.4 Using SAST, DAST, and SCA Together

- **SAST** → early in development and in CI for fast feedback on code.
- **SCA** → in CI on every build to catch vulnerable dependencies.
- **DAST** → on running applications (e.g., staging) to catch runtime and configuration issues.

Together they give broader coverage than any single approach.

### 3.5 Example: SQL Injection and Secure Coding (Java)

**SQL injection** is a common vulnerability: user input is concatenated into SQL and interpreted as part of the query, so an attacker can change the query’s meaning.

#### Vulnerable pattern: `Statement`

```java
// VULNERABLE: User input is concatenated into the query
String userInput = request.getParameter("username"); // e.g. "admin' --"
String query = "SELECT * FROM users WHERE username = '" + userInput + "'";
Statement stmt = connection.createStatement();
ResultSet rs = stmt.executeQuery(query);  // Attacker can break out and run arbitrary SQL
```

If `userInput` is `admin' --`, the query can become `SELECT * FROM users WHERE username = 'admin' --'`, effectively logging in as admin. Worse inputs could drop tables or exfiltrate data.

#### Safer pattern: `PreparedStatement`

```java
// SAFE: Parameters are bound and escaped by the driver
String userInput = request.getParameter("username");
String query = "SELECT * FROM users WHERE username = ?";
PreparedStatement pstmt = connection.prepareStatement(query);
pstmt.setString(1, userInput);  // Treated as data, not as SQL code
ResultSet rs = pstmt.executeQuery();
```

**Why this works:**

- The query structure and the data are **separate**. The database treats the value passed to `?` as **data**, not as executable SQL.
- Special characters in `userInput` are escaped/handled by the driver, so they cannot change the query logic.
- **Bonus:** PreparedStatement is often **faster** (precompiled, cached) and makes code clearer.

**Best practice:** Use **PreparedStatement** (or equivalent parameterized APIs in other languages) for any SQL that includes user or external input. Use plain `Statement` only for fixed, non-user-driven SQL (e.g., some DDL).

---

## 4. Continuous Integration (CI) in DevOps

**Continuous Integration** means frequently merging code into a shared branch and running **automated build and tests** on every change. The goal is to catch integration and quality issues early.

### 4.1 Typical CI Pipeline Steps

A CI pipeline usually includes the following (order can vary by team):

| Step | Description |
|------|-------------|
| **1. Code checkout** | Clone or fetch the latest code from the repository (e.g., Git) for the branch or commit that triggered the pipeline. |
| **2. Dependency management** | Install or restore dependencies (e.g., `npm install`, `pip install -r requirements.txt`, Maven/Gradle dependencies) so the project can build. |
| **3. Linting** | Run static analysis for code style, best practices, and simple bugs (e.g., ESLint, Pylint, SonarQube). Improves consistency and catches issues before tests. |
| **4. Build** | Compile and package the application (e.g., `npm run build`, `mvn package`, `docker build`). Produces artifacts that can be tested and deployed. |
| **5. Testing** | Run automated tests: **unit tests** (fast, isolated), often **integration tests**. Validates behavior and prevents regressions. |
| **6. Security checks** | Run **SCA** (dependencies) and **SAST** (code) in the pipeline. Optionally run **DAST** against a deployed staging build. |
| **7. Artifact creation** | Store build outputs in an artifact repository: JARs, Docker images, npm packages, etc. These are the candidates for deployment. |
| **8. Deployment (to staging)** | Deploy the artifact to a **staging** (or test) environment for further validation (e.g., integration tests, DAST, UAT). |

Pipeline **stages** and **jobs** run on build agents; each job contains **steps** (commands or script actions). Pipelines can be triggered on **commit**, **pull request**, **schedule**, or **manual** run.

### 4.2 Why This Order Matters

- **Checkout → Dependencies → Lint → Build** ensures the code compiles and meets basic quality before expensive tests.
- **Tests and security** run on a consistent build so failures are tied to a specific version.
- **Artifacts** are produced once and reused for staging and production, ensuring “build once, deploy many.”

---

## 5. Continuous Deployment (CD) in DevOps

**Continuous Deployment** extends CI by **automatically deploying** code that passes the pipeline to production (or to the next environment). The goal is to make releases **routine and low-risk**.

### 5.1 How CD Fits With CI

- After the CI steps (build, test, security, artifact creation), the **same artifact** is promoted to staging and then to production.
- **Manual approval** or **gates** (e.g., “all tests green,” “no critical vulnerabilities”) can be required before production.
- When thresholds are met and no blocking issues exist, deployment is **automated**; manual intervention is needed only for exceptions or policy-based approvals.

### 5.2 Benefits

- **Faster time to market** — Features and fixes reach users quickly.
- **Consistency** — Same process and artifact path every time, reducing “works on my machine” and deployment drift.
- **Efficiency** — No long, manual release windows; small, frequent changes reduce risk and rollback scope.

---

## 6. Communication and Coordination in DevOps

In microservices and modern architectures, **how services and clients communicate** is a core concern. DevOps practices often assume and support these patterns.

### 6.1 API Gateway

An **API Gateway** is a single entry point for client requests to backend services. It sits in front of microservices and handles cross-cutting concerns.

| Responsibility | Description |
|----------------|-------------|
| **Routing** | Sends each request to the right microservice (e.g., by path, header, or policy). |
| **Authorization** | Validates tokens (e.g., JWT, OAuth 2.0) and enforces who can call which APIs. |
| **Throttling / rate limiting** | Limits how many requests a client or API can make (e.g., per second, per day) to protect backends and enforce quotas. Returns `429 Too Many Requests` when limits are exceeded. |
| **Other** | SSL termination, request/response transformation, logging, caching. |

**Examples:** AWS API Gateway, Kong, Zuul, Spring Cloud Gateway, Nginx. They can use algorithms like **token bucket** for throttling and **usage plans** for per-client limits.

### 6.2 Inter-Service Communication

Microservices can communicate in two main ways:

#### Synchronous (request–response, blocking)

- One service **calls** another (e.g., HTTP/REST, gRPC) and **waits** for the response.
- **Pros:** Simple, easy to debug, immediate response.
- **Cons:** Tight coupling, cascading failures if a downstream service is slow or down; caller must wait.

**Typical use:** Client → API Gateway → microservice, or service-to-service when an immediate result is needed.

#### Asynchronous (message-based, non-blocking)

- A service **publishes** a message to a **message broker**; other services **consume** it when ready. The sender does not wait for the receiver.
- **Pros:** Loose coupling, better resilience, ability to buffer and retry; supports event-driven and eventual consistency.
- **Cons:** More moving parts, harder to trace end-to-end; eventual consistency and ordering need to be designed.

**Typical use:** Events (e.g., “Order placed”), background jobs, fan-out to multiple consumers.

#### Message brokers: RabbitMQ vs Kafka

| Aspect | RabbitMQ | Apache Kafka |
|--------|----------|--------------|
| **Model** | Message broker: queues and exchanges; messages are consumed and (often) removed. | Event log: messages are persisted and retained; consumers read at their own offset. |
| **Strength** | Task distribution, request–reply, low latency, flexible routing. | High throughput, event streaming, replay, many consumers reading same stream. |
| **Metaphor** | “Post office”: deliver to intended recipients. | “Log/library”: events stored; consumers read when they want. |

**In practice:** Use **synchronous** (e.g., REST) for client–gateway–service and when you need an immediate answer. Use **asynchronous** (e.g., RabbitMQ, Kafka) for events, background processing, and decoupling between internal services.

---

## 7. Conclusion

This class covered:

1. **DevOps** — Practices, culture, and tools that integrate development and operations for faster, more reliable delivery, plus the **challenges** of cultural change.
2. **Concepts** — DevOps philosophy (shared ownership, automation, feedback) and **Shift Left** (testing and security earlier in the SDLC).
3. **Security testing** — **SAST** (code), **DAST** (running app), **SCA** (dependencies), and a **SQL injection** example with **PreparedStatement** in Java.
4. **CI** — Pipeline steps from checkout to build, test, security checks, artifact creation, and deployment to staging.
5. **CD** — Automated deployment when quality and policy thresholds are met.
6. **Communication** — **API Gateway** (routing, auth, throttling) and **inter-service** patterns: **synchronous** (REST/gRPC) vs **asynchronous** (RabbitMQ, Kafka).

Adopting these practices helps organizations **improve delivery speed**, **collaboration**, and **reliability** while keeping security and quality built into the process from the start.