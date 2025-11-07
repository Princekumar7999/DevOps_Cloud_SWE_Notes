Git Refresher
Class date: Friday, 7 November 2025
Topic: Docker basics and containerization — images, containers, daemon, client, volumes, networking, and running containers

1. Introduction to Docker
1.1 What Is Docker?
Docker is a platform for creating and managing containers. It lets you package an application together with its dependencies (runtime, libraries, config) into a standardized unit called a container. That unit can run consistently on any host that has a compatible Docker (or container) runtime.

1.2 Philosophy: "If It Fits, It Ships"
A central Docker idea is "if it fits, it ships." As a developer, you focus on making your code fit inside a container (e.g. via a Dockerfile and a working image). Once the app is packaged as a container image, it can be deployed anywhere that runs Docker (or a compatible runtime)—dev laptop, CI, staging, production, cloud—without "works on my machine" issues. The container is the unit of delivery.

2. Key Concepts in Docker
2.1 Containers
A container is a lightweight, portable, self-contained environment that includes:

Application code
Runtime (e.g. Node, Python, JVM)
System tools and libraries
Settings and configuration
Containers are similar to VMs in that they give an isolated environment, but they are lighter because:

They share the host’s kernel instead of running a full OS per container.
They use namespaces (visibility isolation) and cgroups (CPU, memory, I/O limits) for isolation.
They start in milliseconds and use less disk and memory than VMs.
So a container is effectively a process (or group of processes) that the host kernel isolates; there is no separate “Docker kernel.”

2.2 Images
A Docker image is a read-only template used to create containers. It is built in layers:

Each instruction in a Dockerfile (e.g. RUN, COPY) adds a layer.
Layers are immutable and cached; unchanged layers are reused across builds.
When you run a container, Docker stacks these layers and adds a writable layer on top for the running container; that writable layer is discarded when the container is removed.
Images are the foundation of a container: they hold the application and its dependencies. Images are stored in registries (e.g. Docker Hub) and identified by name and tag (e.g. nginx:latest).

2.3 Docker Daemon and Docker Client
Docker uses a client–server model:

Component	Role
Docker Daemon (dockerd)	Background service on the host. Listens for API requests and manages images, containers, networks, and volumes. It can talk to other daemons (e.g. in Docker Swarm).
Docker Client	CLI (docker) that you use. It turns commands like docker run or docker build into REST API calls and sends them to the daemon. The client and daemon can be on the same machine or the client can target a remote daemon.
Communication: Client and daemon talk over a REST API, typically over a UNIX socket on Linux (e.g. /var/run/docker.sock) or over the network for remote hosts. The Docker Engine API is versioned and can be used by other tools (e.g. curl, SDKs).

2.4 Volumes and Networking
Volumes provide persistent storage for containers. Container filesystem changes in the writable layer are lost when the container is removed; volumes store data outside the container so it survives restarts and removal. Use cases: databases, uploads, config, logs. Docker can create and manage named volumes; you can also use bind mounts (a host path mounted into the container), often for dev (e.g. live code sync). Volumes are the preferred way to persist data in production.

Networking allows containers to talk to each other and to the outside world. Docker provides default networks (e.g. bridge) and you can create custom networks for multi-container apps. Containers can be addressed by name within the same network (Docker’s built-in DNS).

3. Working with Docker
3.1 Running Docker Containers
docker run creates and starts a new container from an image.

Common flags:

Flag	Long form	Meaning
-i	--interactive	Keep STDIN open so you can type into the container.
-t	--tty	Allocate a pseudo-TTY (terminal) in the container.
-d	--detach	Run in the background (detached).
-p	--publish	Map host:container ports (e.g. -p 8080:80).
--name		Give the container a name for easier reference.
-it together is typical for interactive use: you get a proper shell inside the container (e.g. docker run -it ubuntu bash). Without -i you can’t type; without -t you don’t get full terminal behavior (e.g. Ctrl+C). So -it is used when you want to run a shell or an interactive app inside the container.

Examples:

docker run -it ubuntu bash          # Interactive shell in Ubuntu
docker run -d -p 8080:80 nginx      # Nginx in background, host 8080 → container 80
docker run --name myapp myimage     # Named container
3.2 Docker Hub and Image Registries
Docker Hub is a cloud-based registry where you can:

Pull public images (e.g. docker pull nginx).
Push your own images after tagging (e.g. docker tag myapp myuser/myapp:1.0 then docker push myuser/myapp:1.0).
Search for official and community images.
Docker uses Docker Hub as the default registry when you use short image names. Other registries (ECR, GCR, Harbor, etc.) work the same way: you tag with the registry URL and push/pull from there.

3.3 Hands-On and Cloud Shell
The session included hands-on practice (e.g. using Cloud Shell or a similar environment) to:

Run containers with docker run.
Use -it for interactive shells.
Pull images and deploy simple containers.
Practicing these commands reinforces how images, containers, and the client/daemon work together.

4. Docker in Context
Docker remains a core tool for learning containerization. In production, many teams use orchestrators (e.g. Kubernetes) to run and scale containers, but Docker (or a compatible runtime) is still the foundation for building and running container images. Understanding Docker—images, containers, volumes, networking, and the client/daemon model—gives a strong base for moving on to orchestration and more advanced deployment patterns.

5. Summary
Docker packages apps and dependencies into containers; the idea is "if it fits, it ships"—same image runs anywhere with a compatible runtime.
Containers are lightweight and share the host kernel (namespaces + cgroups); images are read-only, layered templates.
Docker Daemon manages objects; the Docker Client (CLI) sends commands via the REST API.
Volumes give persistent storage; networking lets containers communicate.
docker run creates and starts containers; -it is for interactive shells; images come from Docker Hub or other registries.
Docker is essential for learning containerization and forms the basis for tools like Kubernetes.
