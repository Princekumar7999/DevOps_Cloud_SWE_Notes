# Terraform in Action

**Class date:** Monday, 12 January 2026  
**Topic:** AWS and Terraform — subnets, VPC, state, variables, and EC2

---

## 1. Subnets and Dependencies

### 1.1 Subnets in AWS

- A **subnet** is a **range of IP addresses** inside your **VPC**.  
- **Private subnets:** No direct route to the **internet gateway**; typically used for backend resources (e.g. DBs, app servers that don’t need direct inbound internet).  
- **Public subnets:** Have a route to the **internet gateway** and are used for resources that need direct internet access (e.g. load balancers, NAT gateways).

### 1.2 Implicit Dependencies in Terraform

- Terraform infers **dependency order** from **references** between resources.  
- If resource B uses an attribute of resource A (e.g. `aws_subnet.main.id`), Terraform creates **A before B**. You don’t have to declare dependencies explicitly when you reference them.  
- Example: An EC2 instance that uses `subnet_id = aws_subnet.private.id` will be created **after** that subnet.

---

## 2. VPC and Network Configuration

### 2.1 Virtual Private Cloud (VPC)

- A **VPC** is a **virtual network** dedicated to your AWS account, logically isolated from other customers.  
- You define a **CIDR block** for the VPC (e.g. `10.0.0.0/16`) to set the **size and IP range** for the network.

### 2.2 CIDR Blocks

- **CIDR** (Classless Inter-Domain Routing) defines **network prefix** and **size**.  
- Example: `10.0.0.0/16` → 65,536 addresses; `10.0.1.0/24` → 256 addresses. Subnets are usually carved out of the VPC CIDR.

### 2.3 Routing Tables

- **Routing tables** define **how traffic is routed** (e.g. to an internet gateway, to a NAT gateway, or to another subnet).  
- Each subnet is associated with a **route table**; the table’s rules determine where packets go based on **destination** (e.g. `0.0.0.0/0` → internet gateway for public access).

---

## 3. Terraform Basics in Practice

### 3.1 State Management

- Terraform keeps **state** (what it created and its attributes) in a **state file**, by default **terraform.tfstate** in the working directory.  
- For **team** or **shared** use, use **remote state** (e.g. **S3** for the state file and **DynamoDB** for **state locking**) so everyone uses the same state and avoids conflicts.  
- **Never** commit plain `terraform.tfstate` (it may contain secrets); use remote state and restrict access.

### 3.2 Common File Layout

- **main.tf** — main configuration (provider, core resources).  
- **network.tf** — **network** resources: VPC, subnets, internet gateway, route tables.  
- **compute.tf** — **compute** resources: EC2 instances, security groups.  

Splitting by concern keeps configurations easier to navigate and review.

---

## 4. Types of IPs

| Type | Use |
|------|-----|
| **Private IP** | Assigned to resources inside the VPC; reachable only within the VPC (and connected networks). |
| **Public IP** | Assigned to resources that need to be reached from the internet (e.g. EC2 in a public subnet); may change if the instance is stopped/started. |
| **Elastic IP** | A **persistent** public IP tied to your account; you can attach it to an instance and **reassign** it to another, so the “face” of the service doesn’t change when instances are replaced. |

---

## 5. Launching EC2 Instances

- You **cannot** create an EC2 instance without an **AMI** (Amazon Machine Image).  
- The **AMI** defines the **OS and initial software** (e.g. Amazon Linux, Ubuntu).  
- In Terraform you set **ami** (and usually **instance_type**) in the `aws_instance` resource; Terraform will create the instance using that AMI.

---

## 6. Variables in Terraform

- **Input variables** — make configurations **reusable** (e.g. `variable "environment" { }`). Pass values via CLI, file, or env.  
- **Output values** — expose important info (e.g. instance public IP, VPC id) for use by other modules or by operators.  
- **Local values** — **local** to a module/file; computed from variables or other resources, avoid repetition without creating a global variable.

Example: `variable "instance_type" {}`, `output "instance_public_ip" { value = aws_instance.web.public_ip }`, `locals { name_prefix = "myapp-${var.env}" }`.

---

## 7. Summary

- **Subnets:** Public (route to internet gateway) vs **private** (no direct internet); Terraform **implicit dependencies** order creation via resource references.  
- **VPC:** CIDR and routing (route tables) define network layout.  
- **State:** Local or **remote** (e.g. S3 + DynamoDB lock); organize code in **main.tf**, **network.tf**, **compute.tf**.  
- **IPs:** Private, public, **Elastic IP** for stable public endpoints.  
- **EC2:** Requires an **AMI**; use **variables** and **outputs** for flexibility and visibility.