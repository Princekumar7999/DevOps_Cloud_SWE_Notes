Linux Refresher
Class date: Friday, 31 October 2025
Topic: Linux basics in DevOps context, CI/CD pipeline, Docker, and continuous deployment

1. Linux Refresher (Why It Matters for DevOps)
Most production servers, CI/CD runners, and container hosts run Linux. A quick refresher on core concepts helps when configuring pipelines, debugging deployments, and managing artifacts.

1.1 File Permissions
Linux is a multi-user OS; every file has ownership and permissions that control who can read, write, or execute it.

Permission	Symbol	Meaning
Read	r	View file contents (or list directory)
Write	w	Modify file contents (or create/delete in directory)
Execute	x	Run file as program (or enter directory)
Permissions are grouped for three roles:

User (u) — owner of the file
Group (g) — members of the file’s group
Other (o) — everyone else
Example: rwxr-xr-- means owner can read/write/execute, group can read/execute, others can only read.

chmod changes permissions:

Symbolic: chmod u+x script.sh (add execute for owner), chmod g-w file (remove write for group).
Octal: chmod 755 script.sh — owner 7 (rwx), group 5 (r-x), other 5 (r-x). Values: read=4, write=2, execute=1.
In DevOps you’ll use this when making scripts executable, securing config files, or fixing permission errors in containers or CI jobs.

1.2 Shell and Commands
Shell: Command-line interface (often bash) where you run commands and scripts.
Scripts: Automation (e.g., build, deploy, backup) is often done with shell scripts; ensure they have execute permission (chmod +x) and use a shebang (e.g., #!/bin/bash).
Paths: Absolute (/opt/app), relative (./script.sh), and $PATH for finding executables.
These basics support writing and debugging CI steps, Dockerfile commands, and deployment scripts that run on Linux.

2. Introduction to CI/CD
2.1 What Is CI/CD?
CI/CD stands for Continuous Integration and Continuous Deployment. It is the practice of automatically building, testing, and deploying applications so that code changes are integrated and released with minimal manual steps. The goal is to catch issues early and ship frequently and reliably.

2.2 Continuous Integration (CI)
When code is checked in (committed/pushed) to a code repository, it triggers an automated pipeline. The pipeline runs a series of steps to validate and build the application.

Pipeline steps (typical order)
Step	Description
1. Linting	Static checks for code style, syntax, and common bugs. Some organizations skip this, but it is a best practice to run it so issues are caught before the build and before tests.
2. Building	Compiling and packaging the application. This usually includes downloading/installing dependencies (e.g., npm, pip, Maven). Unit testing is often run in this stage if configured.
3. Unit testing	Testing individual units (functions, classes, modules) in isolation. To isolate units that depend on external systems (APIs, DBs), mocking is used: external dependencies are replaced with fake implementations so only the unit under test is exercised.
4. SCA and SAST	Software Composition Analysis (SCA) checks dependencies for known vulnerabilities. Static Application Security Testing (SAST) analyzes source code for security issues. Both are important for meeting security standards and avoiding known vulnerabilities before deployment.
Linting early reduces wasted build time; building and unit tests validate behavior; SCA and SAST address security as part of the pipeline.

Artifacts in CI
Artifacts are the binary (or packaged) outputs of the build. They are what gets tested in later stages and deployed to environments. The form depends on the stack:

Stack / type	Example artifacts
JVM	JARs, WARs
Ruby	Gems
.NET / Windows	EXEs, DLLs
Containers	Docker images
Node / front-end	Bundled JS/CSS, tarballs
Artifacts are stored in an artifact repository (e.g., Nexus, Artifactory, container registry) so the same build can be promoted through testing and production without rebuilding.

3. Introduction to Containers and Docker
3.1 Dockerization
Docker packages an application and its dependencies into a container: a lightweight, runnable unit that is consistent across dev, CI, and production. Dockerization is the process of defining and building such containers.

A Docker image is a static artifact (read-only template) that can be deployed to many environments.
A container is a running instance of an image.
3.2 Creating Docker Images
Images are built from a Dockerfile:

You start from a base image (e.g., node:20, python:3.11).
Each instruction in the Dockerfile (e.g., RUN, COPY, ADD) adds a layer on top of the previous ones.
Layers are cached. If an instruction and its inputs have not changed, Docker reuses the cached layer. If a layer changes, that layer and all layers after it are rebuilt.
Best practice: Put rarely changing steps (e.g., installing system packages) near the top, and frequently changing steps (e.g., copying app code) near the bottom so cache is reused as much as possible.

3.3 Pushing Docker Images
After building, images are tagged (e.g., myapp:1.0, myapp:latest) and pushed to an artifact repository (e.g., Docker Hub, ECR, GCR, Harbor). This gives:

Versioning (tags)
Central storage for the team and pipelines
Promotion of the same image from dev → SIT → production
The CI pipeline typically builds the image, runs tests, then pushes it to the registry so the CD pipeline can pull and deploy it.

4. Continuous Deployment (CD)
4.1 Deployment Pipeline
Deployment can be a separate pipeline or the next stage of the same pipeline after CI:

Input: The artifact (e.g., Docker image or package) produced and stored by CI.
Flow: Pull artifact from the artifact repository → deploy to an environment → run further tests (e.g., SIT) → optionally promote to production.
4.2 System Integration Testing (SIT)
System Integration Testing (SIT) is the stage where the application is deployed to a shared environment and tested together with other systems (databases, APIs, message queues, other services). Unlike unit tests, SIT checks:

End-to-end flows and data passing between components
Interactions with external systems and third-party APIs
Configuration (e.g., env vars, URLs, credentials) in a realistic setup
Integration bugs that only appear when components work together
SIT usually runs on a staging / pre-production platform that mirrors production. Failures here can be used to block or roll back promotion to production.

4.3 Public IPs vs Elastic IPs (Deployment and Stability)
When deploying to cloud (e.g., AWS EC2), IP assignment affects stability of endpoints:

Type	Behavior	Use case
Public IP	Assigned when the instance starts and can change when the instance is stopped and started again.	Temporary or disposable instances; dev/test.
Elastic IP	Stable address that stays associated with the account/instance even across reboots or stops. You can reassign it to another instance.	Production endpoints that must stay fixed (e.g., DNS pointing to a single IP, or legacy systems that cannot use DNS).
Using Elastic IPs (or a load balancer with a fixed DNS name) avoids breaking clients or DNS when instances are replaced or restarted.

5. Summary
Linux underpins most DevOps environments; a refresher on permissions (chmod) and shell/scripts helps when writing and debugging pipelines and deployments.
CI automates lint → build → unit test → SCA/SAST and produces artifacts (JARs, Docker images, etc.) stored in an artifact repository.
Docker builds layered, cached images; images are pushed to a registry for versioning and deployment.
CD takes artifacts from the repository, deploys to environments, and runs SIT to validate integration; Elastic IPs (or similar) help keep production endpoints stable.
Together, CI/CD and Dockerization form a robust delivery pipeline that improves speed, consistency, and quality.