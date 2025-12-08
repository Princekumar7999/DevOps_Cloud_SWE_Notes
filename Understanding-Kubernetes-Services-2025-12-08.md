# Understanding Kubernetes Services

**Class date:** Monday, 8 December 2025  
**Topic:** Kubernetes Services — exposing Deployments and Pods, Service types (ClusterIP, NodePort, LoadBalancer), and how they relate to Pods and Deployments

---

## 1. Why Services?

**Pods** are ephemeral: their **IPs change** when they are recreated. **Deployments** and **ReplicaSets** keep a set of Pods running, but clients need a **stable** way to reach them. A **Service** provides:

- A **stable virtual IP** (ClusterIP) and **DNS name** for a set of Pods.
- **Load balancing** across those Pods (traffic is distributed to ready Pods matching the Service’s selector).

---

## 2. Relationship to Pods and Deployments

- You run **Pods** (directly or via **Deployments**/ReplicaSets).
- A **Service** **selects** Pods by **labels** (e.g. `app: nginx`) and exposes them under one endpoint.
- Deployments ensure the right number of Pods are running; the Service **targets** those Pods and stays stable even when individual Pod IPs change.

---

## 3. Service Types

| Type | Use case | Behavior |
|------|----------|----------|
| **ClusterIP** | Default. Internal-only access. | Cluster-internal virtual IP; only reachable from inside the cluster. |
| **NodePort** | Expose on a fixed port on every node. | Each node listens on `<NodeIP>:<NodePort>` and forwards to the Service → Pods. |
| **LoadBalancer** | Cloud external load balancer. | Cloud provider provisions an external LB (e.g. AWS ELB, GCP LB) that forwards to Nodes/Pods. |

**ClusterIP** is used for in-cluster communication; **NodePort** and **LoadBalancer** expose services to the outside.

---

## 4. YAML and Selectors

- A Service has **spec.selector** (labels) that must **match** the Pods you want to expose (e.g. same labels as in the Deployment’s Pod template).
- **spec.ports** define the port(s) the Service listens on and the **targetPort** (port on the Pod).

Example: a Service that exposes Pods with `app: nginx` on port 80.

---

## 5. DNS

- Kubernetes (CoreDNS) creates **DNS records** for Services. Other Pods can reach a Service by name: **`<service>.<namespace>.svc.cluster.local`** (or short form `<service>` in the same namespace).

---

## 6. Summary

- **Services** give a **stable endpoint** and **load balancing** for Pods selected by labels.
- **ClusterIP** (internal), **NodePort** (per-node port), **LoadBalancer** (cloud LB).
- Services work with **Deployments**: Deployment manages Pods; Service exposes them. Use **declarative YAML** and **namespaces** for clarity and safety.