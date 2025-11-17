# Creating Your Own Docker Images

**Class date:** Friday, 14 November 2025  
**Topic:** Containers fundamentals, history (chroot), Docker vs VMs, namespaces, cgroups, port mapping, and ephemeral nature

---

## 1. Introduction to Containers

**Containers** let you package an application with its dependencies in a **standardized unit**. They provide **isolated environments** on a **shared OS**, giving consistent behavior across development, testing, and deployment. They have become central to modern software delivery.

---

## 2. Historical Background

The idea of isolated environments on one system is not new. A notable predecessor is the Unix **`chroot`** command (late 1960s–early 1970s). **chroot** creates a **"jail"** for processes: it changes the **root directory** for a process and its children, so they see a different subtree of the filesystem. Users can feel they are in a separate environment while still **sharing the same kernel**. Modern containers extend this with **namespaces** and **cgroups** for stronger isolation and resource limits.

---

## 3. Understanding Docker Containers

### 3.1 What Is a Container?

A **Docker container** is a **lightweight, standalone, executable** package that includes everything needed to run a piece of software: code, runtime, libraries, and system tools. Unlike VMs, containers **do not** run a full OS; they **share the host’s kernel** and add only a thin runtime layer.

### 3.2 Containers vs Virtual Machines

| Aspect | Containers | Virtual Machines |
|--------|------------|------------------|
| **Weight** | Lightweight — share OS resources | Heavier — each VM has its own OS |
| **Startup** | Fast (seconds or less) | Slower (boot full OS) |
| **Isolation** | Namespaces + cgroups | Hypervisor + full guest OS |
| **Resource use** | Shared kernel, less overhead | More CPU/memory per instance |

### 3.3 How Containers Work

**Linux namespaces** isolate:

- **Process IDs** — container has its own PID tree  
- **Hostnames** — container can have its own hostname  
- **Network** — own network stack, interfaces, IP  
- **User IDs** — user/group mapping can be isolated  
- **Inter-process communication (IPC)**  
- **Filesystem (mount)** — own mount view  

**Control groups (cgroups)** manage **resource allocation**: CPU, memory, and I/O limits per container so one workload cannot starve others on the same host.

### 3.4 Docker and the Kernel

- **On Linux:** Docker talks **directly** to the kernel (namespaces, cgroups, etc.). No extra VM.
- **On Windows/macOS:** There is no native Linux kernel, so Docker runs a **lightweight Linux VM** (e.g. WSL2, HyperKit) and runs containers inside it.

### 3.5 Running on Different Systems

- **Linux:** Containers run **natively** using the host kernel.  
- **Windows/macOS:** A **Linux VM** is required so that Docker can use a Linux kernel to run containers.

---

## 4. Ephemeral Nature of Containers

Containers are **ephemeral**: they can be started, stopped, and removed without affecting the host. This supports **scaling** and **fault tolerance**, but it means:

- **State inside the container** is lost when the container is removed (unless stored elsewhere).
- Applications should be **stateless** or persist data in **volumes**, object storage, or databases **outside** the container.

---

## 5. Port Mapping and Networking

Containers have their **own network stack** and are isolated. To expose services:

- **Port mapping** links a **host port** to a **container port** (e.g. `docker run -p 8080:80 nginx`). Traffic to the host’s 8080 is forwarded to the container’s 80.
- This is required for **host-to-container** and **external-to-container** access, since direct access to container IPs is limited by design for isolation.

---

## 6. Summary

- **Containers** standardize how we package and run apps; **chroot** was an early form of filesystem isolation.
- **Docker containers** share the host kernel and use **namespaces** (isolation) and **cgroups** (resources); **VMs** use a full OS per instance.
- **Linux:** native; **Windows/macOS:** need a Linux VM for Docker.
- Containers are **ephemeral** — design for stateless apps or **external persistence**.
- **Port mapping** (`-p`) exposes container ports to the host and the network.