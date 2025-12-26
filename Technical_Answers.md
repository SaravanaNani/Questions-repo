# DevOps Interview – Simple One-Go Revision (Q&A)

Purpose: Fast revision before interview  
Style: What + Why, very simple language  
Commands: Only where really required  

---

## 1. Basics

### 1. What is Infrastructure as Code (IaC) and why Terraform?
Terraform allows us to create and manage infrastructure using code. It avoids manual configuration, ensures consistency, and supports multi-cloud environments.

### 2. What is a Terraform state file and why is it important?
The state file stores the mapping between Terraform code and real infrastructure. Without it, Terraform cannot track or update resources.

### 3. Difference between `terraform plan` and `terraform apply`?
`plan` shows what changes will happen. `apply` actually creates or updates infrastructure.

### 4. What is a Docker image and container?
A Docker image is a blueprint. A container is a running instance of that image.

### 5. Why do we use Docker in CI/CD?
Docker ensures the same environment is used in build, test, and deployment stages.

### 6. What is a Jenkins pipeline?
A Jenkins pipeline automates CI/CD stages like build, test, and deploy using code.

### 7. What is Ansible and idempotency?
Ansible automates configuration management. Idempotency means running playbooks multiple times won’t change correct systems.

### 8. Why Kubernetes instead of Docker alone?
Docker runs containers, while Kubernetes manages scaling, self-healing, and rolling updates.

### 9. What is a Pod?
A Pod is the smallest Kubernetes unit that runs one or more containers together.

### 10. Public vs Private subnet?
Public subnets have internet access. Private subnets allow outbound access only through NAT.

---

## 2. Intermediate

### 11. Why use Terraform remote backend?
Remote backend enables shared state, locking, and versioning, allowing safe team collaboration.

### 12. What happens if the Terraform state file is lost?
Terraform cannot track resources. The state must be restored from remote backend versioning.

### 13. What are Terraform modules?
Modules are reusable infrastructure components that reduce duplication and improve consistency.

### 14. How do you manage multiple environments in Terraform?
By using separate environment folders that reuse common modules with different variables.

### 15. Why separate `plan` and `apply` in Jenkins?
To review changes, add approvals, and prevent accidental production issues.

### 16. Why not run Terraform from a laptop?
There is no locking, no audit trail, and environment inconsistency.

### 17. COPY vs ADD in Dockerfile?
COPY is simple and predictable, so it is preferred over ADD.

### 18. Docker volume vs bind mount?
Docker volumes are managed by Docker and are safer for production data.

### 19. How does Jenkins access AWS without access keys?
By using an IAM Role attached to the Jenkins EC2 instance.

### 20. How do you deploy the same artifact across environments?
Build once, store the artifact in Nexus, and deploy the same version everywhere.

### 21. What is a Kubernetes Deployment?
It ensures the desired number of pods are running and supports rolling updates.

### 22. Deployment vs StatefulSet?
Deployment is for stateless applications. StatefulSet is for stateful applications.

### 23. Why do we need a Kubernetes Service?
It provides stable networking because pod IPs change dynamically.

### 24. Service vs Ingress?
Service exposes applications internally, while Ingress manages external HTTP/HTTPS access.

### 25. ConfigMap vs Secret?
ConfigMap stores non-sensitive data, while Secret stores sensitive information.

---

## 3. Advanced

### 26. What is Terraform drift and how do you fix it?
Drift occurs when infrastructure changes outside Terraform. Detect using plan and fix using apply.
```bash
terraform plan
terraform apply
```

### 27. What happens if two users run `terraform apply` at the same time?
State corruption can occur. This is prevented using **state locking** with a remote backend.

### 28. How does DynamoDB help Terraform?
DynamoDB provides **state locking**, ensuring only one Terraform run modifies the state at a time.

### 29. How do Terraform lifecycle rules avoid downtime?
They allow new resources to be created before old ones are destroyed (e.g., `create_before_destroy`).

### 30. Why should Terraform provisioners be avoided?
They are unreliable and break idempotency. **Ansible** should be used for configuration.

### 31. How do you manage secrets securely?
Using **Jenkins Credentials**, **AWS Secrets Manager**, and **IRSA** for Kubernetes.

### 32. How do you update infrastructure without downtime?
By using **rolling updates**, **lifecycle rules**, and **blue-green deployments**.

### 33. How do you reduce Docker image size?
Using **multi-stage builds** and **lightweight base images** (e.g., Alpine).

### 34. Why limit container CPU and memory?
To prevent **resource exhaustion** and improve **cluster stability**.

### 35. What is IRSA?
**IAM Roles for Service Accounts** allow Kubernetes pods to access AWS securely without static credentials.

### 36. How does Kubernetes achieve self-healing?
Kubernetes automatically **restarts failed pods** and maintains desired state.

### 37. What is CrashLoopBackOff and how do you debug it?
It occurs when a container repeatedly crashes.
```bash
kubectl logs pod-name
kubectl describe pod pod-name
```
## 3. Advanced (Continued)

### 38. What are Prometheus, Grafana, and Loki?
Prometheus collects **metrics**, Grafana **visualizes** metrics and logs, and Loki **stores and queries logs**. Together they provide full observability.

### 39. How do you rollback a Helm deployment?
Rollback to the previous stable release using Helm.
```bash
helm rollback release-name revision
```
### 40. How do you scan Docker images for vulnerabilities?
Scan images before deployment to detect security issues.

```
trivy image image-name
```
# 4. Scenario-Based (41–55)

### 41. EC2 instance deleted manually – what do you do?
Terraform detects drift and recreates the resource.

```
terraform plan
terraform apply
```
### 42. Terraform plan shows unexpected changes?
Stop the pipeline, review the plan carefully, and apply only after approval.

### 43. Terraform apply failed halfway – what next?
Fix the issue and rerun terraform apply. Terraform continues safely from the last known state.

### 44. Deployment succeeded but application is not accessible?
Check application logs, ports, security groups, load balancer rules, and rollback if required.

### 45. Maven build fails in Jenkins?
Check Jenkins console logs for dependency, compilation, or test failures.

### 46. Nexus upload fails?
Verify Nexus credentials, check network connectivity, and retry after fixing the issue.

### 47. How do you prevent concurrent deployments?
Disable concurrent builds or use Jenkins locks to allow only one deployment at a time.

### 48. Rollback vs fix-forward?

Rollback if users are impacted. Fix-forward if the issue is minor and clearly understood.

### 49. Pod is running but application is not reachable?

Check Service and Ingress configuration.
```
kubectl get svc
kubectl describe ingress
```
### 50. PVC stuck in Pending state?

Verify storage configuration and availability.
```
kubectl describe pvc
kubectl get storageclass
```
### 51. Ingress not working?
Check ingress controller status, service endpoints, DNS configuration, and TLS certificates.

### 52. Container data lost after restart?
No persistent storage was attached. Use Docker volumes or Kubernetes PVCs.

### 53. Docker image size is too large?
Optimize the Dockerfile, remove unused layers, and use multi-stage builds.

### 54. Node becomes NotReady – what happens?
Kubernetes automatically reschedules pods to healthy nodes.

### 55. What would you improve if redesigning the setup?
Implement blue-green deployments, autoscaling, and stronger monitoring and alerting.
