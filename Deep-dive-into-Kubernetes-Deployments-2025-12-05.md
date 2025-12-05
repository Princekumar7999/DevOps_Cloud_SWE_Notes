# Deep Dive into Kubernetes Deployments

**Class date:** Friday, 5 December 2025  
**Topic:** Deployments, Pods, ReplicaSets, YAML, scaling, rolling updates, rollbacks, and best practices

---

## 1. Key Concepts

### 1.1 Pods and Containers

- **Pods** are the **smallest deployable units** in Kubernetes — a single instance of a running process (or group of containers) in the cluster.
- Each Pod can have **one or more containers**; containers in a Pod share network and storage and are **co-scheduled** on the same node.
- Containers bring **lightweight**, **fast** runtime and the benefits of containerization into Kubernetes.

### 1.2 ReplicaSets

- **ReplicaSets** keep a **specified number of Pod replicas** running. If Pods crash or nodes fail, the ReplicaSet **creates new Pods** so the desired count is restored.
- They provide **scaling** (change replica count) and **high availability** (replace failed Pods).

### 1.3 Deployments

- **Deployments** provide **declarative updates** for applications. They **wrap** a ReplicaSet and add:
  - **Rolling updates** — replace Pods gradually with zero downtime.
  - **Rollbacks** — revert to a previous revision.
- The **Deployment controller** manages ReplicaSets and Pods to match the desired state (image, replicas, strategy).

---

## 2. YAML Configurations

- Both **Deployments** and **ReplicaSets** are defined in **YAML** (or JSON).
- **Deployment** YAML is similar to ReplicaSet; the main difference is **kind: Deployment** and extra fields for **strategy** (e.g. rollingUpdate), **revisionHistoryLimit**, etc.

**Example Deployment YAML:**

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-deployment
spec:
  replicas: 3
  selector:
    matchLabels:
      app: nginx
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
      - name: nginx
        image: nginx:1.16.1
```

---

## 3. Scaling and Rollout Strategies

- **Scaling:** Change **replicas** (e.g. `kubectl scale deployment nginx-deployment --replicas=5`) or edit the YAML and re-apply.
- **Rolling updates:** Deployments use a **rolling update** strategy by default: new Pods are created and old ones are terminated gradually so there is **no downtime** during updates.

---

## 4. Upgrade and Rollback

- **Upgrade:** Change the image (or template) and **kubectl apply**. The Deployment creates a new ReplicaSet and scales it up while scaling down the old one.
- **Rollback:** **kubectl rollout undo deployment/<name>** (or **rollout history** and **rollout undo --to-revision=N**) reverts to a previous revision. Essential for **CI/CD** when a release needs to be reverted.

---

## 5. Best Practices

- **Declarative config:** Use **YAML** in version control so changes are traceable and auditable.
- **Namespaces:** Use **namespaces** to separate environments (dev, staging, prod).
- **Monitor desired vs actual state:** Use `kubectl get`, `kubectl describe`, and **events** to ensure the cluster matches what you defined.
- **Prefer YAML over imperative commands in production:** Keeps a single source of truth and easier rollbacks/audits.

---

## 6. Summary

- **Deployments** manage **ReplicaSets** and **Pods** with **rolling updates** and **rollbacks**.
- Define them in **YAML**; use **kubectl apply** and **kubectl scale**; use **rollout** for updates and rollbacks.
- Combine with **Services** to expose Deployments inside and outside the cluster.