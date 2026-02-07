Class date: Monday, 27 October 2025
Topic: Introduction to Microservices and DevOps

1. Introduction to Microservices
1.1 What Are Microservices?
Microservices are an architectural style that structures an application as a collection of loosely coupled services. Each service is:

Fine-grained — focused on a single business capability or domain
Lightweight in terms of protocols — typically HTTP/REST or message queues
Autonomous — can be developed, deployed, and scaled independently
This contrasts with monolithic architecture, where the entire application is built as one single unit. In a monolith, all components are tightly coupled, share one codebase and deployment, and scale together. Microservices break the application into smaller, independent services that can be deployed and evolved separately.

1.2 Benefits of Microservices
Benefit	Description
Scalability	Different parts of the application can be scaled independently. High-traffic components can get more resources without scaling the whole system, which is more efficient and cost-effective.
Flexibility in technology stack	Each microservice can be built with different programming languages, frameworks, or databases. Teams can choose the best tool for each specific task (e.g., Python for ML, Go for performance-critical services).
Independent deployment	Services can be released on their own schedule. This enables faster, more frequent releases and reduces the risk of one change breaking the entire application.
1.3 Key Concepts in Microservices
REST API communication
Microservices talk to each other over REST APIs. This approach:

Uses HTTP/HTTPS as the transport, so services can interact regardless of the programming language or platform.
Keeps interfaces contract-based (e.g., request/response formats), which supports loose coupling and independent evolution of services.
Statelessness
Microservices are designed to be stateless:

They do not store session or application state inside the service itself.
State is kept in databases, caches, or message queues (external data stores).
This makes services easy to replicate and scale horizontally — any instance can handle any request.
Externalized logging
Because the system is distributed, logging cannot live only inside each service:

Logs are centralized and externalized (e.g., sent to a log aggregation system).
This is essential to trace what happened across services, especially when something fails.
Common tools: Fluentd, ELK Stack (Elasticsearch, Logstash, Kibana), Splunk — used for collecting, storing, and searching logs.
Load balancing and DNS
Load balancer: Distributes incoming traffic across multiple instances of a service. It improves availability and stability by avoiding overloading a single server and by routing around failed instances.
DNS (Domain Name System): Resolves human-readable domain names to IP addresses. It is important because names are easier to remember and manage than raw IPs, and DNS can be used to route traffic (e.g., to different regions or environments).
2. DevOps Introduction
2.1 Why DevOps Matters
DevOps brings together Development (Dev) and IT Operations (Ops) so that:

The software development lifecycle is shortened.
Continuous delivery becomes possible with high software quality.
Collaboration, automation, and shared responsibility replace silos between dev and ops.
2.2 Continuous Integration and Continuous Deployment (CI/CD)
Practice	What it means
Continuous Integration (CI)	New code changes are frequently merged into a shared repository. Each merge triggers automated build and tests, so issues are caught early and the main branch stays stable.
Continuous Deployment (CD)	Code that passes CI is automatically deployed to production (or to staging first). Deployments become routine, low-risk events instead of big, manual releases.
Together, CI and CD form a CI/CD pipeline: from code commit to build, test, and deployment, with as much automation as possible. The class highlighted building a complete CI/CD pipeline and using appropriate tools.

2.3 Relation to Microservices
DevOps practices (CI/CD, automation, monitoring, logging) are especially important in a microservices world because:

Many services need to be built, tested, and deployed often.
Manual processes do not scale; automation and clear pipelines are necessary.
3. Course Context and Expectations
3.1 Upcoming Topics
Docker — containerization; packaging applications and their dependencies into portable, consistent units.
Kubernetes — orchestration of containers; deployment, scaling, and management of containerized applications.
These are core tools in the DevOps and microservices ecosystem.

3.2 How You Will Be Assessed
Assignments and class participation are part of the assessment.
Daily notes (e.g., submitted on GitHub) are encouraged to build discipline and reinforce learning.
Hands-on sessions will follow theory to give practical experience with the concepts.
3.3 Class Dynamics
Active participation and consistent note-taking are encouraged.
Emphasis is on continuous learning and applying what you learn in practice.
4. Summary
This class introduced:

Microservices — loosely coupled, independently deployable services with benefits in scalability, technology choice, and deployment speed.
Core microservices concepts — REST APIs, statelessness, externalized logging, load balancing, and DNS.
DevOps — integration of dev and ops, with CI/CD as a central practice for fast, reliable delivery.
Course direction — Docker, Kubernetes, assignments, and hands-on work to support learning.
These ideas form the foundation for building and operating scalable, modern applications.