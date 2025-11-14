Docker Images
Class date: Wednesday, 12 November 2025
Topic: Docker images, Dockerfiles, build process, CMD vs ENTRYPOINT, multi-stage builds, and performance optimization

1. Introduction
Docker is a platform for automating deployment, management, scaling, and operations of application containers. This class focused on image creation, Dockerfiles, and commands to manage containers effectively.

2. Docker Images and Containers
2.1 Docker Image
A Docker image is more than a simple template. It is a packaged environment for your application: it contains everything needed for the app to run (base OS, runtime, dependencies, code, config). Images are the building blocks of containers — immutable, layered, and reusable.

2.2 Docker Container
A container is an isolated environment where a Docker image runs — analogous to a lightweight VM. Containers are lightweight because they share the host kernel (via namespaces and cgroups), which makes them fast to start and efficient in resource use.

3. Basic Commands and Usage
3.1 Docker Build
docker build creates a Docker image from a Dockerfile.

Syntax: docker build [options] <build context>
-t (tag): Assigns a name and optional tag to the image, e.g. docker build -t myapp:1.0 .
Convention: Use lowercase for image and tag names to avoid registry issues.
3.2 Docker Run
docker run starts a container from an image.

-d — run in detached (background) mode.
-p <host>:<container> — port forwarding so you can access container ports from the host.
Example: docker run -d -p 8080:80 nginx
4. Dockerfile Essentials
A Dockerfile is a script of instructions used to assemble an image. Each instruction usually adds a layer.

4.1 FROM
FROM <image>[:tag] — sets the base image (starting point) for the build. All subsequent instructions build on top of this layer.
Example: FROM node:20-alpine, FROM ubuntu:22.04
4.2 RUN
RUN <command> — runs commands during the build (inside a temporary container). Each RUN creates a new layer.
Best practice: Combine related commands with && to reduce layers and keep the image smaller (e.g. RUN apt-get update && apt-get install -y package).
4.3 CMD and ENTRYPOINT
Both define the default command when a container starts. Difference:

CMD	ENTRYPOINT
Override	Yes — arguments passed to docker run replace CMD.	No — arguments passed to docker run are appended to ENTRYPOINT.
Typical use	Default arguments for the container.	Fixed executable; user args are passed to it.
Example: ENTRYPOINT ["nginx"] and CMD ["-g", "daemon off;"] — running docker run myimg -v passes -v to nginx. If you used only CMD, docker run myimg something would replace the whole command.
Use JSON array form (["exec", "arg1"]) for proper signal handling and no shell interpolation.
5. Advanced Concepts
5.1 Multi-Stage Builds
Multi-stage builds use multiple FROM instructions in one Dockerfile. You build in one stage (e.g. compile with JDK) and copy only the needed artifacts into a later stage (e.g. run with JRE). The final image excludes build tools and intermediates, so image size is reduced and performance is improved.

Example: Stage 1: FROM maven → build JAR. Stage 2: FROM eclipse-temurin:17-jre → copy JAR, set CMD. Final image has only the JRE and the JAR.

5.2 Copy-on-Write (CoW)
Docker uses a copy-on-write mechanism: only the layers that are needed are loaded and modified at runtime. This optimizes storage and performance — containers share read-only layers and have a thin writable layer on top.

6. Performance Optimization
6.1 Reducing Layers
Fewer layers generally mean smaller images and faster builds. Combine RUN commands with && and use a single RUN for a logical step (e.g. install several packages in one RUN).
Order instructions so rarely changing steps (e.g. base, dependencies) come first and frequently changing steps (e.g. copying app code) come last to maximize cache reuse.
6.2 Base Image Choice
Slim/Alpine base images reduce size and attack surface (e.g. node:20-alpine, eclipse-temurin:17-jre-alpine).
For Java: use JRE (not full JDK) in the production image to reduce size; use JDK only in the build stage (e.g. in multi-stage builds).
7. Practical Example
The class used a Java application with multi-stage builds: first stage with JDK to compile, second stage with JRE and only the built JAR. This keeps the production image small and secure.

8. Tools and Environment
The session included guidance on setting up a Docker environment, creating containers, and deploying applications. Practicing these commands in a real setup is important to internalize Docker’s workflow.

9. Summary
Images are packaged environments; containers are where they run, sharing the host kernel.
docker build -t builds from a Dockerfile; docker run -d -p runs and exposes ports.
Dockerfile: FROM (base), RUN (build-time commands, minimize layers), CMD (overridable default), ENTRYPOINT (fixed executable).
Multi-stage builds reduce image size; CoW optimizes runtime; fewer layers and right base image (e.g. JRE for Java) improve performance.