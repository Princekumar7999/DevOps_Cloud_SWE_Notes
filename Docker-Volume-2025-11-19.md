# Docker Volume (Docker Networking)

**Class date:** Wednesday, 19 November 2025  
**Topic:** Docker networking — IPs, subnetting, bridge (docker0), custom networks, and troubleshooting

---

## 1. Overview

**Docker networking** enables **communication between containers** and with the **outside world**. Understanding IP addressing, subnetting, and how Docker’s default and custom networks work is essential for building and debugging containerized applications.

---

## 2. Key Concepts

### 2.1 IP Addresses

An **IP address** (Internet Protocol address) **uniquely identifies** a device on a network. In Docker, each container on a network gets a **unique IP** within that network so other containers and the host can reach it.

### 2.2 Subnetting

**Subnetting** divides a network into **smaller networks**. **CIDR** (Classless Inter-Domain Routing) notation defines the prefix and how many bits are for hosts.

- **Example:** `192.168.10.0/24` — 24 bits are the network; 32 − 24 = **8 bits** for hosts → 2^8 = **256** addresses (typically 254 usable for hosts).
- **/26** → 64 addresses per subnet. Example: `192.168.10.0/26`, `192.168.10.64/26`, `192.168.10.128/26` for separate subnets (e.g. Engineering, HR, Reception).

### 2.3 Network ID and Broadcast ID

- **Network ID:** The **first** address in a subnet (e.g. `192.168.10.0`) — identifies the network itself; not assigned to a host.
- **Broadcast ID:** The **last** address in a subnet (e.g. `192.168.10.255` for /24) — used to send traffic to **all** hosts in the subnet.

---

## 3. Docker Default Bridge (docker0)

When Docker is installed, a default network interface **docker0** (bridge) is created. It behaves like a **virtual switch**:

- Containers can communicate with **each other** and with the **external network** through this bridge.
- Each container has an **eth0** interface connected to the bridge via a **veth pair** (virtual Ethernet pair): one end in the container’s network namespace, the other in the host’s namespace attached to the bridge.

**Default bridge network:**

- Every container can be run in the **default** bridge network (e.g. `docker run -d nginx`).
- You can also create **custom** bridge networks with `docker network create -d bridge my_bridge` and optionally specify **subnets** and **gateways** for clearer organization and isolation.

---

## 4. Example: Network Setup

- **Default:** Containers on the default bridge (`docker0`) can talk to each other using their **IP addresses** (and to the outside world via NAT).
- **Custom bridge:** `docker network create -d bridge my_bridge` (optionally `--subnet` and `--gateway`). Containers attached to `my_bridge` get IPs from that subnet and can resolve each other by **name** if you use Docker’s embedded DNS.
- **Practice scenario:** Subnet a larger range (e.g. `192.168.10.0/24`) into smaller ones: e.g. `/26` for Engineering (`192.168.10.0/26`), HR (`192.168.10.64/26`), Reception (`192.168.10.128/26`).

---

## 5. Containers Interacting

- Containers on the **same** network can reach each other by **IP** (and often by **container name** on user-defined networks).
- Docker uses **virtual bridges** and **routing** defined in each container’s network settings. Use **`docker inspect <container_id>`** to see network details (IP, gateway, network ID).

---

## 6. Troubleshooting

- **`docker inspect <container_id>`** — inspect network configuration (IP, network name, gateway).
- **`docker network ls`** — list networks.
- **`docker network inspect <network_name>`** — see containers attached and subnet info.
- Verify **subnet/gateway** and that containers are on the **same network** when they should communicate.

---

## 7. Summary

- **IP addresses** identify containers; **subnetting** (CIDR) defines network size; **network ID** and **broadcast ID** are first and last addresses.
- **docker0** is the default bridge; containers connect via **veth pairs**; you can create **custom bridge** networks with optional subnets.
- Use **docker inspect** and **docker network** commands to troubleshoot connectivity and configuration.