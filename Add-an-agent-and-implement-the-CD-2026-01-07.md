# Add an Agent and Implement the CD

**Class date:** Wednesday, 7 January 2026  
**Topic:** CI/CD with Kubernetes — custom runners, environment setup (Vagrant/VM), and deploying to a cluster

---

## 1. Introduction

This class focused on **deploying applications into a Kubernetes cluster** as part of **Continuous Integration** and **Continuous Deployment**, and on using **custom runners** and automation to support that workflow.

---

## 2. CI vs CD

### 2.1 Continuous Integration (CI)

- **Automates** the integration of code from multiple contributors into one project.  
- **Goals:** Frequent merges, **automated testing**, and keeping the main codebase in a **deployable** state.  
- Output: built and tested **artifacts** (e.g. JAR, Docker image).

### 2.2 Continuous Deployment (CD)

- **Extends** automation to **deploying** changes into a **production** (or staging) environment.  
- Involves **infrastructure** and **deployment** mechanisms (e.g. kubectl, Helm, Argo CD) to deliver updates with little or no downtime.  
- In well-structured setups, **CI** and **CD** are often **separate** processes: CI produces artifacts; CD consumes them and deploys. This keeps responsibilities clear and allows different gates (e.g. security, approvals) for deployment.

---

## 3. Setting Up a Kubernetes Environment

### 3.1 Virtual Machine and Cluster

- You need at least a **one-node** (or multi-node) **Kubernetes cluster**.  
- **VirtualBox** and **Vagrant** can be used to automate the creation of VMs with the right OS and resources.  
- **Vagrant** scripts let you copy-paste (or version-control) the definition of the environment so everyone gets the same setup.

### 3.2 Pre-Installed Kubernetes

- Having an **AMI** (or similar) or **Vagrant box** with **Kubernetes already installed** (e.g. via kubeadm) is a practical shortcut so you don’t reinstall every time.

---

## 4. GitHub Actions and Custom Runners

### 4.1 GitHub Actions

- GitHub Actions **automates** CI/CD workflows in the repo: build, test, package, release, and **deploy** from GitHub.  
- Workflows run on **GitHub-hosted runners** (default) or on **self-hosted (custom) runners**.

### 4.2 Custom (Self-Hosted) Runners

- **Custom runners** are machines **you** manage and **register** with GitHub. They run your workflow jobs instead of GitHub’s VMs.  
- **Why use them:** Access to your **private network**, **Kubernetes** (kubectl), or other tools only available on your side; no need to expose clusters to the internet.  
- **Setup:** On a machine (Linux is typical), run the runner **registration** (e.g. script or `config.sh`) provided by GitHub (**Settings → Actions → Runners**). Configure **labels** and **security** (e.g. network, permissions) as needed.  
- Once registered, use that runner in the workflow with **runs-on: [self-hosted, linux]** (or your custom labels).

---

## 5. Project Work and Teamwork

- The class included a **project**: implement **CI/CD** for an application using **GitHub Actions** (and related tools).  
- **Teams** build and present the project; each person is responsible for **their own contributions** and should be able to explain them (e.g. during presentations or Q&A).

---

## 6. Testing and Quality

- Use a **structured testing** approach across environments, for example:  
  - **System Integration Testing (SIT)**  
  - **Performance testing**  
  - **Security testing**  
- Automate these where possible to reduce human error and improve coverage.

---

## 7. Practical Takeaways

- **CI** and **CD** are often **separate** in mature setups — different pipelines or stages, with CD gated by CI success and possibly approvals.  
- **CD** configuration depends on your **application** and **hosting** (e.g. Kubernetes, cloud services).  
- **Custom runners** are useful when the deployment target (e.g. cluster) or secrets are only reachable from your own network.

---

## 8. Summary

- **CI** = automated build and test; **CD** = automated (or gated) deployment to environments.  
- **Kubernetes** can be set up on VMs (e.g. **Vagrant** + VirtualBox) or using pre-built images/AMIs.  
- **GitHub Actions** can use **self-hosted runners** to run jobs that need access to your cluster or internal tools.  
- Combine **CI/CD** with **structured testing** (SIT, performance, security) and clear separation between CI and CD for production-style workflows.