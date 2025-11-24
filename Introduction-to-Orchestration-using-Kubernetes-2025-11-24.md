# Introduction to Orchestration Using Kubernetes

**Class date:** Monday, 24 November 2025  
**Topic:** Kubernetes overview — Pods, control plane, data plane, features, scaling, and certificates

---

## 1. Overview

**Kubernetes** is an open-source platform for **automating deployment, scaling, and operations** of application containers across **clusters of hosts**. It provides resilience, scalability, and operational efficiency for distributed, containerized applications.

---

## 2. Key Concepts

### 2.1 Pods

- **Definition:** A **Pod** is the **smallest deployable unit** in Kubernetes. It represents one or more **containers** that are **scheduled together** on the same node and share resources (network, storage).
- **Creation and management:** When you create a Pod, the **control plane** schedules it onto a **worker node** (data plane). The control plane **assigns** the work but does **not** run the workload; the **nodes** do.

---

## 3. Kubernetes Architecture

The architecture is split into **control plane** and **data plane**.

### 3.1 Control Plane

| Component | Role |
|-----------|------|
| **API Server** | Frontend of the control plane. Handles **internal and external** requests via a **REST API**. All cluster changes go through it. |
| **etcd** | **Consistent, distributed key-value store** holding **all cluster state** (desired and current state of objects). |
| **Controller (Controller Manager)** | **Reconciliation:** watches desired state vs actual state and tries to fix drift (e.g. ensure desired number of replicas are running). |
| **Scheduler** | Decides **which node** a Pod runs on based on resources, constraints, and policies. |

### 3.2 Data Plane (Worker Nodes)

| Component | Role |
|-----------|------|
| **Kubelet** | Agent on **each node**. Ensures **containers** are running in Pods as specified; reports node and Pod status to the API server. |
| **Container Runtime (CRI)** | Runs containers (e.g. containerd, CRI-O). Docker is an example of a runtime; Kubernetes uses the **CRI** to talk to runtimes. |
| **Kube-proxy** | Maintains **network rules** on the node (e.g. forwarding traffic to Pods for Services). |

---

## 4. Key Features

- **Automatic bin packing:** Schedules Pods onto nodes based on **resource requests/limits** and constraints while trying to maintain availability.
- **Self-healing:** Restarts failing containers, replaces/reschedules Pods when nodes die, kills containers that fail **health checks**.
- **Horizontal scaling:** Scale applications **up or down** via commands, UI, or **autoscaling** (e.g. based on CPU).
- **Service discovery and load balancing:** Pods get **IPs** and **DNS**; **Services** provide a **stable endpoint** and load balancing across Pods.
- **Automated rollouts and rollbacks:** Update application configuration with **zero downtime**; roll back if needed.
- **Secret and configuration management:** Deploy and update **Secrets** and **ConfigMaps** without rebuilding images or hardcoding config.
- **Batch execution:** Support for **Jobs** and **CronJobs** (batch and scheduled workloads).

---

## 5. Deployment and Scaling

- **Horizontal Pod Autoscaler (HPA):** Adjusts **number of Pod replicas** based on metrics (e.g. CPU utilization).
- **Vertical Pod Autoscaler (VPA):** Adjusts **CPU/memory requests and limits** of Pods.
- **Cluster Autoscaler:** Adjusts **number of nodes** in the cluster when Pods are **pending** due to insufficient resources.

---

## 6. Practical Insights

- **Certificates:** Kubernetes can manage **certificates** dynamically for authentication, reducing reliance on static credentials.
- **Pod lifecycle:** When a Pod is **deleted**, Kubernetes ensures associated containers are **terminated** and resources are released.

---

## 7. Summary

- **Pods** are the smallest deployable unit; control plane **schedules** them, data plane **runs** them.
- **Control plane:** API Server, etcd, Controller, Scheduler. **Data plane:** Kubelet, CRI, Kube-proxy.
- Features include **self-healing**, **scaling** (HPA, VPA, Cluster Autoscaler), **Services**, **rollouts/rollbacks**, and **Secrets/ConfigMaps**.