# Docker Containers (Docker Volumes)

**Class date:** Monday, 17 November 2025  
**Topic:** Docker volumes — persistence, types (named, anonymous, bind mounts), and volume management

---

## 1. Introduction to Docker Volumes

**Docker volumes** are Docker’s built-in way to provide **persistent storage** for containers. Data in a volume **outlives** the container’s lifecycle, so it is not lost when a container is removed or recreated. Volumes can also be **shared** among containers, which helps with data sharing and decoupling storage from the container lifecycle.

---

## 2. Importance of Volumes

- **Databases:** Data must survive container restarts and replacements. Mounting a volume for the DB data directory keeps it persistent.
- **Decoupling:** Storage is separated from the container; you can replace or upgrade the container without losing data.
- **Flexibility:** Volumes can be backed up, migrated, and attached to different containers.

---

## 3. Volume Concepts

### 3.1 Ephemeral Nature of Containers

Containers are **ephemeral**: when they stop or are removed, by default any **data written inside the container filesystem** is lost. Volumes (and bind mounts) keep that data on the **host** (or in Docker-managed storage) so it persists.

### 3.2 Writable Layer

Every container has a **writable layer** on top of the read-only image layers. Changes to the filesystem (new files, modified files) are stored in this layer. When a container is **stopped and restarted** (without being removed), the writable layer is still there. When the container is **removed**, the writable layer is discarded — so for long-term data, use **volumes** (or bind mounts).

---

## 4. Types of Docker Volumes

### 4.1 Named Volumes

- **Managed by Docker.** You give the volume a name.
- Stored in a Docker-managed path on the host (often under `/var/lib/docker/volumes/`).
- **Reusable** across containers: multiple containers can use the same named volume.
- Create: `docker volume create <volume_name>`
- Best for: production data, databases, shared data between containers.

### 4.2 Anonymous Volumes

- Same idea as named volumes but **no user-defined name** (Docker assigns an ID).
- Created automatically when you use `-v /container/path` without a name.
- **Not intended** for reuse across containers; harder to manage and reference.

### 4.3 Bind Mounts

- **Not** Docker-managed. You **map a specific host path** to a path in the container.
- The container reads/writes **directly** to the host filesystem. You are responsible for the path, permissions, and structure.
- **Use cases:** development (live code sync), injecting config files, when you need direct host access.
- Syntax: `-v /host/path:/container/path` or `--mount type=bind,source=/host/path,target=/container/path`

---

## 5. Volume Management

### 5.1 Creating a Volume

```bash
docker volume create <volume_name>
```

Creates a named volume that Docker manages.

### 5.2 Inspecting a Volume

```bash
docker volume inspect <volume_name>
```

Shows where the volume is stored on the host and other metadata.

### 5.3 Attaching a Volume to a Container

```bash
docker run -v <volume_name>:<container_path> <image_name>
```

Example: `docker run -v mydata:/var/lib/mysql mysql` — the container’s `/var/lib/mysql` is backed by the volume `mydata`.

Alternative: `--mount type=volume,source=<volume_name>,target=<container_path>`

### 5.4 Read-Only Volumes

To allow a container to **only read** from a volume (e.g. config or read-only data), append **`:ro`**:

```bash
docker run -v <volume_name>:<container_path>:ro <image_name>
```

The container cannot modify the volume contents.

---

## 6. Summary

- **Volumes** provide **persistent** and **shared** storage independent of container lifecycle.
- **Named volumes** — Docker-managed, reusable, good for production.
- **Anonymous volumes** — no name, auto-created; use when you don’t need to reference the volume by name.
- **Bind mounts** — host path mapped into container; you manage the path and permissions.
- **Create:** `docker volume create`; **attach:** `-v name:path` or `--mount`; **read-only:** `:ro`.