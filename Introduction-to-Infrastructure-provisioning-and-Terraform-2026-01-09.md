# Introduction to Infrastructure Provisioning and Terraform

**Class date:** Friday, 9 January 2026  
**Topic:** Terraform and Infrastructure as Code — lifecycle commands, advantages, and supporting tools

---

## 1. Introduction to Terraform

**Terraform** is an open-source tool for **managing cloud (and other) infrastructure as code**. You define resources in **configuration files** (`.tf`), and Terraform **plans** and **applies** changes to match that desired state across providers (AWS, Azure, GCP, etc.).

### 1.1 Key Characteristics

- **Platform-agnostic:** Works with many **providers** (AWS, Azure, Google Cloud, etc.) via plugins, so you can manage hybrid or multi-cloud infrastructure from one tool.  
- **Infrastructure as Code (IaC):** Infrastructure is defined in **code** (versioned, reviewable, repeatable), which reduces manual errors and keeps environments **consistent**.

---

## 2. Terraform Lifecycle Commands

Four core commands:

### 2.1 terraform init

- **Purpose:** Initializes the **working directory** that contains your Terraform configuration.  
- **Actions:** Downloads **providers** and **modules** declared in the config; prepares the backend (local or remote).  
- **Version locking:** Can lock provider versions for **reproducibility**.  
- Run **once** per directory (and again if you change backend or add providers/modules).

### 2.2 terraform plan

- **Purpose:** Compares the **current state** of infrastructure (from the state file) with the **desired state** (from your `.tf` files) and produces an **execution plan**.  
- **Output:** What Terraform would **add**, **change**, or **destroy** — **no changes are applied** yet.  
- Use it to **review** changes and avoid surprises before applying.

### 2.3 terraform apply

- **Purpose:** Applies the **planned** changes so that real infrastructure matches the desired state.  
- Typically runs **plan** again (unless you use a saved plan) and then prompts for confirmation (or use **-auto-approve** in automation).  
- Use in a **controlled** way (e.g. in CI with approval or after review).

### 2.4 terraform destroy

- **Purpose:** **Destroys** the resources managed by the current Terraform configuration.  
- Frees cloud resources and avoids lingering cost; use with care (especially in production).

---

## 3. Advantages of Terraform

- **Speed and efficiency:** Automates creation and updates; much faster and less error-prone than manual setup.  
- **Consistency:** Same code produces the same infrastructure; reduces drift between environments.  
- **Version control:** Store `.tf` files in **Git** for history, code review, and rollback of config changes.  
- **Documentation:** The code itself documents what exists and how it’s wired.

---

## 4. Supporting Tools and Concepts

### 4.1 Configuration Management

- Tools like **Ansible**, **Puppet**, and **Chef** manage **software configuration** and updates **on** existing servers (packages, files, services).  
- Terraform is best at **provisioning** and **lifecycle** of infrastructure (VPCs, instances, load balancers). The two are **complementary**: Terraform creates the box; Ansible/Puppet/Chef configure it.

### 4.2 Server Templating and Containers

- **Vagrant** — creates and manages **virtual environments** (e.g. local VMs) from code; useful for dev/test environments.  
- **Docker** — **container** images as immutable units; Terraform can work with container-related resources (e.g. ECS, EKS) or you use Terraform for the underlying infrastructure and other tools for container images.

---

## 5. Practical Example: EC2 with Terraform

- **Define** infrastructure in `.tf` files (e.g. AWS provider, VPC, subnet, security group, **EC2 instance**).  
- Run **terraform init** → **terraform plan** → **terraform apply** to create/update resources.  
- Use **Terraform state** (e.g. `terraform.tfstate`) so Terraform knows what it manages and can update or destroy resources correctly.  
- **terraform destroy** removes the defined resources when they’re no longer needed.

---

## 6. Conclusion

Terraform is a central tool for **Infrastructure as Code**: it provides **speed**, **consistency**, and **version control** for infrastructure and fits into modern DevOps practices. Use the official Terraform docs for advanced use cases and additional commands.