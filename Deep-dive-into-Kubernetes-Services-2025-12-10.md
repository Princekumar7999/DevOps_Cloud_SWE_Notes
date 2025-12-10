# Deep Dive into Kubernetes Services (Networking Model)

**Class date:** Wednesday, 10 December 2025  
**Topic:** Kubernetes networking — API Server, controllers, Pods, nodes, container-to-container, Pod-to-Pod, Pod-to-Service, DNS, egress, and ingress

---

## 1. Kubernetes Basics

### 1.1 API Server

- **Central** control plane component; exposes the **REST API**.
- Backed by **etcd** (cluster state). In Kubernetes, **everything** is an API call to the API server (create/update/delete/get objects).

### 1.2 Controllers

- **Watch** the API (desired state), compare with **actual state**, and **reconcile** (e.g. Scheduler assigns Pods to nodes; Kubelet on each node runs containers and configures networking).

### 1.3 Pods

- **Smallest deployable unit**; one or more containers.
- Containers in a Pod **share**: **network namespace** (same IP, same localhost), **volumes**.

### 1.4 Nodes

- **Worker** machines (VMs or bare metal). Run **kubelet**, **kube-proxy**, and the **container runtime** (containerd, CRI-O).

---

## 2. Kubernetes Networking Model (Three Rules)

Kubernetes enforces:

1. **All Pods** can talk to **all other Pods** without NAT.
2. **All Nodes** can talk to **all Pods** without NAT.
3. The **IP a Pod sees** is the **same** as the IP others use to reach it (no NAT identity change).

So the stack must handle: **container-to-container**, **Pod-to-Pod**, **Pod-to-Service**, and **Internet-to-Service**.

---

## 3. Container-to-Container (Inside a Pod)

- Containers in a Pod **share one network namespace** (one IP, shared ports, shared loopback).
- They communicate over **localhost** (same machine from the network’s perspective).

---

## 4. Pod-to-Pod Networking

- Each Pod has its **own** network namespace. A **veth pair** connects the Pod’s **eth0** to the **root** namespace; the host end is attached to a **Linux bridge** (e.g. **cbr0**).
- **Same node:** Pod → eth0 → veth → bridge → ARP → destination veth → destination Pod.
- **Different nodes:** Each node typically has a **Pod CIDR** (e.g. Node1: 10.0.1.0/24, Node2: 10.0.2.0/24). Traffic for another node’s Pod goes: Pod → bridge → node’s default interface → network → destination node → bridge → destination Pod. **CNI** (e.g. AWS VPC CNI, Calico, Flannel, Weave) handles how Pod IPs are assigned and routed.

---

## 5. Pod-to-Service (ClusterIP)

- Pod IPs **change** when Pods are recreated. A **Service** provides a **stable virtual IP** (ClusterIP) and **load balancing** to backend Pods.
- **Routing:** **kube-proxy** implements this:
  - **iptables (classic):** Rules match traffic to Service IP, then **DNAT** to a chosen backend Pod; return traffic is **SNAT**’ed back (conntrack).
  - **IPVS (modern default):** Kernel load balancer; more scalable; creates a dummy interface and binds the Service IP.
- **DNS:** **CoreDNS** creates records for Services: **`<svc>.<ns>.svc.cluster.local`**. Pods resolve the name and get the ClusterIP (or directly to Pod IPs depending on setup).

---

## 6. Internet-to-Service

**Egress (Pod → Internet):** The internet gateway usually only knows **node** IPs. **iptables SNAT** rewrites the **source** from Pod IP to **Node IP** so return traffic comes back to the node, which then forwards to the Pod.

**Ingress (Internet → Pods):**

1. **Service type LoadBalancer (L4):** Cloud provider creates an external load balancer (e.g. AWS ELB, GCP LB). LB forwards to **NodePort** (or directly to nodes), then to Service → Pods.
2. **Ingress controller (L7):** NGINX Ingress, AWS ALB Ingress, Traefik, Istio Gateway, etc. Provide **host-based** and **path-based** routing, **SSL termination**. Ingress → **Services** → Pods.

---

## 7. Summary Table

| Layer | What’s happening |
|-------|------------------|
| Container-to-Container | Same network namespace → **localhost** |
| Pod-to-Pod (same node) | veth → bridge → veth |
| Pod-to-Pod (cross node) | Routed via **Pod CIDR** and CNI |
| Pod-to-Service | **iptables/IPVS** DNAT to backend Pods; **CoreDNS** for name resolution |
| Egress | **SNAT** Pod IP → Node IP → Internet |
| Ingress | **LoadBalancer** (L4) or **Ingress controller** (L7) → Service → Pods |