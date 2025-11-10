Introduction to Docker
Class date: Monday, 10 November 2025
Topic: Docker and containerization — running containers, basic commands, images, networking, and Dockerfile layers

1. Introduction to Docker and Containers
1.1 What Is Docker?
Docker is an open platform for developing, shipping, and running applications. It lets you separate your applications from your infrastructure so you can deliver software quickly. You manage infrastructure in the same way you manage applications: as code (Dockerfiles, compose files) and as versioned, portable artifacts (images).

1.2 Running Applications in Containers
Containers package an application with everything it needs to run: code, runtime, libraries, and dependencies. That package is shipped as one unit, so the application runs reliably in any environment (dev, CI, staging, production) that supports the container runtime. "Build once, run anywhere" is the goal.

2. Basic Docker Commands
2.1 Running a Container
docker run creates and starts a new container from an image.

Example: docker run -it ubuntu bash — starts a new Ubuntu container and gives you an interactive terminal (-i = keep STDIN open, -t = allocate a TTY).
Other useful flags: -d (detached/background), -p host:container (port mapping), --name (container name).
2.2 Entering an Existing Container
docker exec runs a command inside a container that is already running. Unlike docker run, it does not create a new container.

Example: docker exec -it <container_id> bash — opens a shell in the running container.
Use when you need to debug, inspect files, or run one-off commands inside a live container.
2.3 Inspecting Containers
Command	Purpose
docker ps	List running containers (use -a for all, including stopped).
docker container inspect <container_id>	Detailed JSON info about the container: config, network (including IP address), mounts, state.
Knowing the container IP is useful when you need to reach one container from another (e.g. curl http://<container_ip>:port).

3. Working with Docker Images
3.1 Creating and Managing Images
Start from an existing image (e.g. ubuntu), run a container, make changes inside it, then capture those changes as a new image with docker commit.
docker images — lists images on your machine (repository, tag, size).
docker commit -m "message" <container_id> <new_image_name> — creates a new image from the current state of the container. Use sparingly; Dockerfiles are the preferred, reproducible way to build images.
3.2 Pushing Images to Docker Hub
docker push <image_name> — uploads the image to a registry (e.g. Docker Hub).
For Docker Hub, the image name must start with your Docker Hub username (e.g. myuser/myapp:tag). Tag first with docker tag <local_image> myuser/myapp:tag, then push.
Log in with docker login if needed.
4. Networking and Container Communication
Containers get their own IP address on a Docker network. To find it: docker container inspect <container_id> and look at the network section (e.g. Networks → IPAddress).
With the IP, you can communicate between containers (e.g. curl http://<container_ip>:port) or from host to container if ports are published.
For production, prefer named networks and service discovery (e.g. container names, or an orchestrator like Kubernetes) instead of relying on raw IPs, which change when containers are recreated.
5. Dockerfile and Image Layers
Images are usually built from a Dockerfile: a script of instructions that define a multi-step build. Each instruction typically adds a new layer to the image.
Layers are cached. If an instruction and its context haven’t changed, Docker reuses the cached layer. Understanding layers helps you order instructions (e.g. copy dependency files before app code) and reduce image size by combining RUN steps.
When you commit a container to a new image, you are effectively saving a new layer on top of the base image with your changes.
6. Practical Usage
6.1 Detached vs Attached Mode
docker run -d — runs the container in detached (background) mode. The terminal returns immediately; the container keeps running.
Without -d, the container runs in the foreground (attached). Logs go to your terminal; Ctrl+C stops the container (unless configured otherwise).
6.2 Containers vs Virtual Machines
Docker does not carry the overhead of a full OS per container. It uses the host OS kernel (on Linux) and isolates workloads with namespaces and cgroups. Result: faster startup, lower resource use, and higher density than VMs.
Multi-architecture: Be aware of ARM vs AMD (x86). Images are architecture-specific unless you build multi-platform images. Use consistent architecture for image build and deployment to avoid compatibility issues.
7. Summary
Docker separates app from infrastructure and delivers software via containers.
docker run starts new containers; docker exec runs commands in running containers; docker ps and docker container inspect help you list and inspect them.
Images can be created with docker commit or (preferrably) Dockerfiles; docker push uploads to a registry (e.g. Docker Hub — use username in the image name).
Networking: use inspect to get container IPs for communication; plan for IP changes when containers are recreated.
Layers and caching matter for efficient Dockerfile builds; detached mode (-d) runs containers in the background.