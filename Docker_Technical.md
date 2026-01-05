# 🐳 Docker – Interview Questions & Answers (Interview Preparation)

## 1️⃣ Docker Fundamentals

**1. What is Docker and why is it used?**  
Docker is a containerization platform used to package applications with all their dependencies so they run consistently across development, testing, and production environments.

**2. Difference between Virtual Machines vs Containers?**  
Virtual Machines run a full OS on a hypervisor, making them heavy and slow to start. Containers share the host OS kernel, making them lightweight, fast, and resource-efficient.

**3. What problem does Docker solve in DevOps?**  
Docker solves the “it works on my machine” problem by providing consistent environments across all stages of the CI/CD pipeline.

**4. What is containerization?**  
Containerization is the process of packaging an application, its libraries, and configuration into a single runnable unit called a container.

**5. Explain Docker architecture.**  
Docker architecture consists of Docker Client (CLI), Docker Daemon (engine that builds and runs containers), and Docker Registry (stores images).

**6. What is Docker Engine?**  
Docker Engine is the core service that builds images, runs containers, and manages container lifecycle.

**7. Explain Docker Client, Docker Daemon, Docker Registry.**  
Docker Client sends commands, Docker Daemon executes them, and Docker Registry stores Docker images.

**8. What is Docker Desktop vs Docker Engine?**  
Docker Desktop is a GUI tool for Windows/Mac. Docker Engine is the runtime used on servers and Linux systems.

**9. What is the role of containerd?**  
containerd manages container lifecycle tasks such as starting, stopping containers and pulling images.

**10. How does Docker use Linux kernel features?**  
Docker uses namespaces for isolation and cgroups for resource management.

**11. What is a Docker image?**  
A Docker image is a read-only template used to create containers.

**12. What is a Docker container?**  
A Docker container is a running instance of a Docker image.

**13. Difference between Image vs Container?**  
Image is a blueprint; container is the running application.

**14. What happens when you run docker run?**  
Docker pulls the image if not present, creates a container, assigns networking/storage, and starts it.

**15. What is an image layer?**  
Each instruction in a Dockerfile creates a layer, enabling caching and reuse.

**16. Why are Docker images immutable?**  
Images cannot be modified after creation; changes require rebuilding, ensuring consistency and traceability.

**17. What is a Dockerfile?**  
A Dockerfile contains instructions to build a Docker image.

**18. Common Dockerfile instructions?**  
FROM, RUN, CMD, ENTRYPOINT, COPY, ADD.

**19. Difference between CMD and ENTRYPOINT?**  
CMD provides default arguments that can be overridden. ENTRYPOINT defines the main command that always runs.

**20. What is a multi-stage build?**  
A build technique that separates build and runtime stages to reduce final image size.

**21. Why should we avoid running containers as root?**  
Running as root increases security risks and potential host compromise.

**22. What is .dockerignore and why is it important?**  
It excludes unnecessary files from the build context, reducing image size and build time.

**23. What are Docker network types?**  
Bridge, Host, None, Overlay.

**24. Difference between Bridge vs Host network?**  
Bridge isolates containers using NAT. Host shares the host’s network directly.

**25. How do containers communicate with each other?**  
Using Docker networks and container DNS names.

**26. How does Docker DNS work?**  
Docker provides internal DNS to resolve container names within the same network.

**27. What happens when you expose a port?**  
It maps container ports to host ports for external access.

**28. Difference between Volume, Bind mount, tmpfs?**  
Volume is Docker-managed storage, bind mount maps host directories, tmpfs stores data in memory.

**29. When would you use Docker volumes?**  
When data must persist across container restarts.

**30. Where are Docker volumes stored?**  
Inside Docker’s storage directory on the host.

**31. What happens to container data after container deletion?**  
Data is lost unless stored in volumes.

**32. How do you limit CPU and memory for a container?**  
docker run --cpus="1.0" -m 512m myapp

**33. What happens when a container exceeds memory limits?**  
It is killed by the kernel (OOMKilled).

**34. What are Docker namespaces and cgroups?**  
Namespaces isolate resources; cgroups control resource usage.

**35. How do you secure Docker containers?**  
Use non-root users, minimal images, network isolation, and vulnerability scanning.

**36. What is Docker Content Trust?**  
It ensures only signed and trusted images are pulled and run.

---

## 2️⃣ Docker Compose

**37. What is Docker Compose?**  
A tool for defining and running multi-container applications using a YAML file.

**38. Difference between docker-compose vs docker run?**  
docker-compose manages multiple services; docker run runs a single container.

**39. What is depends_on in Docker Compose?**  
Controls startup order of services.

**40. Can Docker Compose be used in production?**  
Generally not recommended; Kubernetes is preferred for production.

---

## 3️⃣ Docker Registry & Distribution

**41. What is Docker Hub?**  
A public and private container image registry.

**42. Difference between public registry vs private registry?**  
Public allows anonymous access; private requires authentication.

**43. How do you push images to a private registry?**  
docker login → docker tag → docker push.

**44. How do you tag Docker images properly?**  
Use meaningful version tags instead of latest.

---

## 4️⃣ Docker Best Practices

**45. How do you reduce Docker image size?**  
Use Alpine images, multi-stage builds, dockerignore, and fewer layers.

**46. Why use Alpine images?**  
They are lightweight and reduce attack surface.

**47. Why should we minimize layers?**  
Fewer layers result in smaller images and faster builds.

**48. How do you scan Docker images for vulnerabilities?**  
Use tools like Trivy during CI/CD.

---

## 5️⃣ Scenario-Based Docker Questions (Detailed)

**49. Container keeps crashing — how do you troubleshoot it?**  
Check container logs first to identify application errors. Verify CMD/ENTRYPOINT, environment variables, and required dependencies. Run the container interactively to debug startup issues. Common causes include misconfiguration, missing configs, or application crashes.

**50. Docker image size is too large — how do you reduce it?**  
Analyze the Dockerfile, switch to Alpine or slim base images, use multi-stage builds to exclude build tools, remove package caches, and use dockerignore to avoid copying unnecessary files.

**51. Container restarts and data is lost — why and how to fix?**  
Containers are stateless by default, so filesystem data is lost on restart. Use Docker volumes or external storage to persist data outside the container lifecycle.

**52. Container is running but application not accessible — what do you check?**  
Verify port mappings, ensure the application is listening on the correct interface (0.0.0.0), check firewall/security groups, and validate network configuration.

**53. How do containers communicate securely with each other?**  
Use private Docker networks, avoid exposing ports publicly, rely on internal DNS, and optionally use TLS for encrypted communication.

**54. How do you pass secrets to containers securely?**  
Pass secrets at runtime using environment variables, Docker secrets, Kubernetes secrets, or external secret managers instead of hardcoding them in images.

**55. How do you update a running container without downtime?**  
Run a new container version alongside the old one, shift traffic using a load balancer, then stop the old container. In production, orchestration tools handle this automatically.

**56. A container is using 100% CPU — how do you debug?**  
Use docker stats to identify resource usage, exec into the container to inspect running processes, analyze application behavior, and apply CPU limits to prevent host impact.

**57. A vulnerability is found in your Docker image — what do you do?**  
Identify the vulnerable package, update the base image or dependency, rebuild the image, rescan for vulnerabilities, push a new version, and redeploy.

**58. How do you integrate Docker into CI/CD pipelines?**  
Build the image in CI, run tests, scan for vulnerabilities, push to a registry, and deploy using orchestration tools. Docker ensures consistent environments across all stages.

---

## 6️⃣ Advanced Docker Questions (Detailed)

**59. Difference between Docker Swarm and Kubernetes?**  
Docker Swarm is simpler and easier to set up but limited in features. Kubernetes provides advanced scheduling, scaling, self-healing, and is the industry standard for container orchestration.

**60. What happens if Docker daemon crashes?**  
Docker commands stop working and containers may stop. Restart policies can automatically restart containers once the daemon recovers.

**61. Explain rootless Docker.**  
Rootless Docker allows containers to run without root privileges, improving security and reducing risk of host compromise.

**62. How does Docker handle logging?**  
Docker captures container stdout/stderr and supports multiple logging drivers such as json-file, syslog, fluentd, and cloud-native log services.

**63. What are distroless images?**  
Distroless images contain only the application and runtime, removing shells and package managers, resulting in smaller and more secure images.

**64. How do you debug containers in production?**  
Start with logs and metrics, use exec access cautiously, reproduce issues in lower environments, and avoid making direct changes in production containers.

**65. Explain OCI (Open Container Initiative).**  
OCI defines open standards for container image formats and runtimes, ensuring compatibility between tools like Docker, containerd, and Kubernetes.
