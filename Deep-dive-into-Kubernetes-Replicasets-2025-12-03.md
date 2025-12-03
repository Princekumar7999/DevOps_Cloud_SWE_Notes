# Deep Dive into Kubernetes ReplicaSets

**Class date:** Wednesday, 3 December 2025  
**Topic:** Kubernetes YAML structure, Pods, ReplicaSets, selectors, controllers, and basic kubectl commands

---

## 1. Kubernetes YAML Structure

Most Kubernetes YAML resources have **four main parts**:

| Field | Purpose |
|-------|---------|
| **apiVersion** | API version (e.g. `apps/v1`, `v1`) for compatibility and feature support. |
| **kind** | Type of object: Pod, ReplicaSet, Deployment, Service, etc. |
| **metadata** | Data about the object: **name** (usually required), namespace, labels, annotations. |
| **spec** | **Specification** of what you want; structure depends on **kind** (e.g. Pod spec vs ReplicaSet spec). |

---

## 2. Understanding Pods

- A **Pod** is the **smallest deployable unit**: one or more **containers** that are **co-located** and **co-scheduled** on the same node.
- When a **container** in a Pod **crashes**, Kubernetes can **restart** it (or replace the Pod if restart policy is exceeded). When a **Pod** is gone, a **ReplicaSet** (or Deployment) ensures a **new Pod** is created to maintain the desired count — like “replacing the hero” each time.

---

## 3. YAML Syntax Basics

- **Key-value:** `key: value`
- **List:** `- item1` then `- item2`
- **Map:** Nested key-value; values can be lists or other maps.

Indentation and spaces matter in YAML; no tabs.

---

## 4. ReplicaSets in Detail

### 4.1 Purpose

A **ReplicaSet** ensures a **specified number of Pod replicas** are **running at any time**. If Pods crash or nodes fail, the ReplicaSet **creates new Pods** (on available nodes) so the **current state** matches the **desired state** (replica count).

### 4.2 Structure of a ReplicaSet YAML

- **kind: ReplicaSet**
- **spec.replicas** — desired number of Pods (e.g. `100`).
- **spec.selector** — **label selector** that defines which Pods this ReplicaSet **owns**. It must match the labels in the **Pod template**.
- **spec.template** — the **Pod template** (metadata + spec) used to create new Pods when more are needed.
- **Labels** on the template (and selector) are used to **manage** Pods as a group, similar to tags in cloud environments.

### 4.3 Internal Working

- **Kubelet** on each node runs Pods and reports status to the API server.
- **API server** authenticates, authorizes, and validates requests; state is stored in **etcd**.
- **Controllers** (including the ReplicaSet controller) **watch** the API for desired state, compare with actual state, and **reconcile** (e.g. create/delete Pods).
- **Scheduler** assigns **new Pods** to nodes based on resources and constraints.

### 4.4 Pod Lifecycle and Recovery

When the **desired** replica count does not match the **current** count (e.g. a Pod failed), the ReplicaSet **controller** detects the discrepancy and asks the API server to **create** (or delete) Pods until the count is correct.

---

## 5. Practical Commands

| Command | Purpose |
|---------|---------|
| **kubectl apply -f &lt;file&gt;** | Create or update resources from a YAML file. |
| **kubectl delete -f &lt;file&gt;** | Delete resources defined in the file. |
| **kubectl get &lt;resource&gt;** | List resources (e.g. `kubectl get pods`, `kubectl get replicaset`). |

---

## 6. Summary

- Kubernetes YAML: **apiVersion**, **kind**, **metadata**, **spec**.
- **Pods** are the smallest unit; **ReplicaSets** maintain a **fixed number** of Pod replicas using a **selector** and **template**.
- **Controllers** and **scheduler** work with the **API server** and **etcd** to keep desired state; use **kubectl apply/delete/get** to manage resources.