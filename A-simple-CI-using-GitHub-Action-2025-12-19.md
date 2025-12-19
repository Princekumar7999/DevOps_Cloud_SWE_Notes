# A Simple CI Using GitHub Action

**Class date:** Friday, 19 December 2025  
**Topic:** Introduction to GitHub Actions — workflows, jobs, steps, and a basic Java/Maven build

---

## 1. What Is GitHub Actions?

**GitHub Actions** lets you run **automated tasks** when events happen in your repository. Workflows are defined in **YAML** and run on GitHub-hosted or self-hosted **runners**.

**Examples:**

- When code is **pushed** → build the app.
- When a **pull request** is created → run tests.

---

## 2. Project Structure (Simple Java App)

```
java-github-actions-demo/
├── src/
│   └── Main.java
├── pom.xml
└── .github/
    └── workflows/
        └── build.yml
```

- **`.github/workflows/`** — folder name must be **exact**; this is where GitHub looks for workflow files.

---

## 3. Application and Maven Config

### 3.1 Simple Java Class

**`src/Main.java`**

```java
public class Main {
    public static void main(String[] args) {
        System.out.println("Hello from GitHub Actions!");
    }
}
```

### 3.2 Maven Configuration (pom.xml)

- **groupId**, **artifactId**, **version** — identify the project.
- **maven.compiler.source** and **maven.compiler.target** — set Java version (e.g. 17). Must **match** the Java version used in the workflow (e.g. `setup-java` with `java-version: '17'`).

Example snippet:

```xml
<properties>
    <maven.compiler.source>17</maven.compiler.source>
    <maven.compiler.target>17</maven.compiler.target>
</properties>
```

---

## 4. GitHub Actions Concepts

| Term | Meaning |
|------|---------|
| **Workflow** | The automation — a YAML file in `.github/workflows/`. |
| **Job** | A set of **steps** that run on the same runner. |
| **Step** | A single action or shell command. |
| **Runner** | The machine that runs the job (e.g. `ubuntu-latest`, Windows, macOS). |
| **Action** | Reusable unit (e.g. `actions/checkout`, `actions/setup-java`). |

---

## 5. Example Workflow: Java Build

**File:** `.github/workflows/build.yml`

```yaml
name: Java Build Workflow          # Name in GitHub Actions UI
on:
  push:
    branches:
      - main                        # Trigger on push to main
jobs:
  build:
    runs-on: ubuntu-latest          # Linux VM
    steps:
      - name: Checkout code
        uses: actions/checkout@v4    # Clone repo into runner
      - name: Set up JDK 17
        uses: actions/setup-java@v4
        with:
          java-version: '17'
          distribution: 'temurin'
      - name: Build with Maven
        run: mvn clean package      # Compile and create JAR
```

### Step-by-Step

| Step | What it does |
|------|----------------|
| **Checkout** | `actions/checkout@v4` — downloads your repo into the runner. Without it, there is no code to build. |
| **Set up JDK 17** | Installs Java 17 (Eclipse Temurin) so `mvn` and the compiler are available. |
| **Build with Maven** | Runs `mvn clean package` — compiles and packages the app (e.g. JAR in `target/`). |

---

## 6. What Happens When You Push?

1. GitHub detects a **push to main**.
2. A **runner** (Linux VM) is allocated.
3. **Checkout** → code is on the runner.
4. **Java** is installed.
5. **Maven** runs; if it succeeds → **green check**; if it fails → **red X** and logs.

**Where to see results:** Repo → **Actions** tab → select the workflow run → open the **job** and **steps** to view logs.

---

## 7. Common Beginner Mistakes

| Mistake | Fix |
|---------|-----|
| Java version mismatch | Use the same version in **pom.xml** and in **setup-java** (e.g. 17). |
| Workflow not running | Ensure folder is **`.github/workflows`** (exact name) and YAML is valid. |
| YAML errors | Use **spaces** (e.g. 2) for indentation; avoid **tabs**. |
| Maven not found | Add the **Set up JDK** step (e.g. `actions/setup-java`) before the Maven step. |

---

## 8. Summary

- **GitHub Actions** runs automated tasks (workflows) on **events** (e.g. push to main).
- **Workflow** = YAML file; **job** = group of steps; **step** = one action or command; **runner** = machine (e.g. `ubuntu-latest`).
- For a **simple Java CI**: **checkout** → **setup Java** → **mvn clean package**. Keep **Java version** in pom.xml and workflow in sync.