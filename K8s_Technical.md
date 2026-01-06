# 📘 Kubernetes Interview Questions & Answers (Quick Recap – Simple & Interview-Ready)

---

## 1️⃣ Kubernetes Fundamentals

### 1. What is Kubernetes and why is it used?
Kubernetes is a container orchestration platform used to deploy, scale, and manage containerized applications automatically.

### 2. What problems does Kubernetes solve that Docker alone cannot?
Docker runs containers, but Kubernetes manages scaling, self-healing, load balancing, rolling updates, and high availability.

### 3. Explain Kubernetes architecture at a high level.
Kubernetes has a control plane that manages the cluster and worker nodes that run application pods.

### 4. What is a cluster in Kubernetes?
A cluster is a group of control plane and worker nodes that run containerized applications.

### 5. Difference between containerization vs orchestration?
Containerization packages apps; orchestration manages and coordinates containers at scale.

---

## 2️⃣ Kubernetes Architecture (Control Plane & Nodes)

### 6. Explain the role of:
- **API Server** – Entry point for all Kubernetes operations  
- **etcd** – Distributed key-value store for cluster state  
- **Scheduler** – Assigns pods to nodes  
- **Controller Manager** – Ensures desired state is maintained  

### 7. What happens if etcd goes down?
The cluster cannot read/write state; workloads keep running but no changes can be made.

### 8. What is a node?
A worker machine where pods run.

### 9. What components run on a worker node?
kubelet, kube-proxy, container runtime.

### 10. What is kubelet?
An agent that ensures containers defined in pods are running on the node.

---

## 3️⃣ Pods & Containers

### 11. What is a Pod?
The smallest deployable unit in Kubernetes, containing one or more containers.

### 12. Why is Pod the smallest deployable unit?
Containers in a pod share network, storage, and lifecycle.

### 13. Can a Pod have multiple containers? When?
Yes, for sidecar patterns like logging or monitoring.

### 14. What happens when a Pod dies?
Kubernetes recreates it if managed by a controller.

### 15. Difference between:
- **Pod vs Deployment** – Pod runs once; Deployment manages replicas  
- **Pod vs ReplicaSet** – ReplicaSet ensures pod count  

---

## 4️⃣ Workloads

### 16. What is a Deployment?
Manages stateless applications with scaling and rolling updates.

### 17. Difference between:
- **Deployment vs StatefulSet** – Stateless vs stateful apps  
- **Deployment vs DaemonSet** – App pods vs one pod per node  
- **DaemonSet vs Job** – Continuous vs one-time task  
- **Job vs CronJob** – One-time vs scheduled jobs  

### 18. What is a ReplicaSet?
Ensures a specified number of pod replicas are running.

### 19. How does Kubernetes maintain desired state?
Using controllers that continuously reconcile actual state with desired state.

---

## 5️⃣ Services & Networking

### 20. What is a Service?
An abstraction that exposes pods using a stable IP and DNS.

### 21. Explain Service types:
- **ClusterIP** – Internal access  
- **NodePort** – Exposes via node IP  
- **LoadBalancer** – Cloud load balancer  
- **ExternalName** – Maps to external DNS  

### 22. Difference between ClusterIP vs NodePort?
ClusterIP is internal; NodePort exposes externally via node IP.

### 23. How does Pod-to-Pod communication work?
Direct communication using cluster networking (CNI).

### 24. What is kube-proxy?
Routes service traffic to backend pods.

### 25. How does Kubernetes DNS work?
CoreDNS maps service names to ClusterIP addresses.

---

## 6️⃣ Ingress & Traffic Management

### 26. What is Ingress?
Manages external HTTP/HTTPS access to services.

### 27. Difference between Service vs Ingress?
Service exposes pods; Ingress routes traffic to services.

### 28. What is an Ingress Controller?
A controller that implements Ingress rules.

### 29. Name popular Ingress controllers.
NGINX, AWS ALB, Traefik.

### 30. How do you expose multiple services with one LoadBalancer?
Using Ingress with routing rules.

---

## 7️⃣ Config & Secrets

### 31. What is a ConfigMap?
Stores non-sensitive configuration data.

### 32. What is a Secret?
Stores sensitive data like passwords.

### 33. Difference between ConfigMap vs Secret?
Secrets are base64 encoded and protected.

### 34. How do you mount secrets into pods?
As environment variables or volumes.

### 35. Why should secrets not be stored in Git?
Risk of exposure and security breaches.

---

## 8️⃣ Storage

### 36. What is a Volume?
Storage attached to a pod.

### 37. Difference between emptyDir vs hostPath?
emptyDir is temporary; hostPath uses node storage.

### 38. What is a PersistentVolume (PV)?
Cluster-wide storage resource.

### 39. What is a PersistentVolumeClaim (PVC)?
Request for storage by a pod.

### 40. Difference between PV vs PVC?
PV is supply; PVC is demand.

### 41. What is a StorageClass?
Defines dynamic storage provisioning.

---

## 9️⃣ Scheduling & Scaling

### 42. What is the Kubernetes scheduler?
Assigns pods to nodes.

### 43. What is HPA?
Automatically scales pods based on metrics.

### 44. What metrics does HPA use?
CPU, memory, custom metrics.

### 45. Difference between HPA vs VPA?
HPA scales pods; VPA scales pod resources.

### 46. What is Cluster Autoscaler?
Scales worker nodes based on demand.

---

## 🔟 Security & Access Control

### 47. What is RBAC?
Controls access using roles and permissions.

### 48. Difference between Role vs ClusterRole?
Role is namespace-scoped; ClusterRole is cluster-wide.

### 49. What is a ServiceAccount?
Identity for pods to access APIs.

### 50. What is PodSecurity (PSA)?
Controls pod security standards.

### 51. How do you secure a Kubernetes cluster?
RBAC, network policies, secrets management, private nodes.

---

## 🔥 Scenario-Based Questions

### 52. Pod in CrashLoopBackOff – how to debug?
Check logs, describe pod, verify config.

### 53. Pod running but not accessible?
Check service, ingress, ports, security rules.

### 54. Deploy new version without downtime?
Use rolling updates or blue-green.

### 55. Pods stuck in Pending?
Insufficient resources or scheduling constraints.

### 56. ConfigMap updated but not reflected?
Pods must be restarted.

### 57. Manage secrets securely in production?
Use Secrets, Vault, cloud secret managers.

### 58. Namespace consuming too many resources?
Apply ResourceQuota and LimitRange.

### 59. Node goes down – what happens?
Pods are rescheduled on healthy nodes.

### 60. Upgrade Kubernetes safely?
Upgrade control plane first, then nodes gradually.

### 61. Manage Dev/QA/Prod environments?
Use namespaces, separate clusters, or GitOps.

---

## 🔷 Advanced Kubernetes Questions

### 62. What is etcd quorum?
Majority of etcd nodes required for consistency.

### 63. How does Kubernetes achieve self-healing?
Restarts and reschedules failed pods automatically.

### 64. What are CRDs?
Custom resource types in Kubernetes.

### 65. What is an Operator?
Automates lifecycle of complex apps using CRDs.

### 66. What is a Service Mesh?
Manages service-to-service communication.

### 67. Kubernetes vs Docker networking?
K8s provides flat networking across pods.

### 68. Debug DNS issues?
Check CoreDNS pods and config.

### 69. What is an Admission Controller?
Validates or mutates requests before persistence.

### 70. What is a Pod Disruption Budget?
Limits voluntary pod disruptions.

### 71. What is OOS (Out of Sync)?
Desired and actual state mismatch.

### 72. What is QoS?
Defines pod priority based on resource requests.

---

## 📌 DNS & Headless Services

### 73. What is Kubernetes DNS?
Service discovery mechanism via CoreDNS.

### 74. What is a Headless Service?
Service without ClusterIP for direct pod access.

### 75. What DNS records are used?
A, AAAA, and CNAME records.

---
# 📘 Kubernetes & AWS EKS Interview Questions and Answers (Quick + Detailed, 2 Years Experience)

This section **adds the missing AWS EKS–specific questions** and **integrates them cleanly** with the Kubernetes recap you already have.  
All answers are **simple, interview-friendly, and real-world focused**.

---

## 1️⃣ Kubernetes & Amazon EKS Basics

### 1. What is Kubernetes and why is it used?
Kubernetes is a container orchestration platform used to deploy, manage, and scale containerized applications. Docker runs individual containers, but Kubernetes manages containers across many machines with features like self-healing, load balancing, rolling updates, rollbacks, and auto-scaling.

### 2. What is Amazon EKS?
Amazon EKS is a **managed Kubernetes service** on AWS. AWS manages the control plane (API Server, etcd, Scheduler), while customers manage worker nodes or use AWS Fargate. This reduces operational overhead and improves security and reliability.

## 2️⃣ Pods, Deployments & Services

### 4. What is a Pod?
A Pod is the smallest deployable unit in Kubernetes. It can contain one or more containers that share network and storage. Kubernetes manages Pods, not individual containers.

### 5. Why does Kubernetes use Pods instead of containers directly?
Pods allow multiple containers (like app + sidecar) to work together as one unit. Pods are ephemeral, so higher-level controllers like Deployments manage availability.

### 6. What is a Deployment?
A Deployment manages stateless applications. It ensures the desired number of Pods are running and supports rolling updates and rollbacks.

### 7. What is a Kubernetes Service?
A Service provides a stable IP and DNS name to access Pods. Since Pod IPs change, Services use labels to route traffic reliably.

### 8. Types of Kubernetes Services
- ClusterIP – Internal access only
- NodePort – Exposes via node IP and port
- LoadBalancer – Uses cloud load balancer
- ExternalName – Maps to external DNS

---

## 3️⃣ Ingress, TLS & cert-manager

### 9. What is Ingress?
Ingress manages external HTTP/HTTPS access to services. It works with an Ingress Controller and supports path-based and host-based routing.

### 10. What is cert-manager?
cert-manager automates TLS certificate creation and renewal using CAs like Let’s Encrypt. It stores certificates as Kubernetes Secrets and is commonly used with Ingress.

---

## 4️⃣ Storage in EKS

### 11. What are PV and PVC?
- PV (PersistentVolume): Storage resource in the cluster
- PVC (PersistentVolumeClaim): Request for storage by a Pod  
Pods use PVCs, and Kubernetes binds them to matching PVs.

### 12. What is a StorageClass?
A StorageClass defines how storage is dynamically provisioned. In EKS, it works with the **EBS CSI Driver** to automatically create EBS volumes.

---

## 5️⃣ Security & IAM (EKS Specific)

### 13. What is IRSA?
IRSA (IAM Roles for Service Accounts) allows Kubernetes service accounts to assume IAM roles. This enables pods to securely access AWS services without static credentials.

---

## 6️⃣ Troubleshooting Scenarios (VERY IMPORTANT)

### 14. How do you troubleshoot a PVC stuck in Pending state?
Check:
1. Pod events
2. PVC status
3. StorageClass
4. EBS CSI driver pods
5. IAM permissions (IRSA)
6. AZ mismatch between node and volume

### 15. How do you troubleshoot Ingress not working?
Check in order:
1. Pod health
2. Service selectors/endpoints
3. Ingress rules
4. Ingress Controller pods
5. LoadBalancer and DNS
6. TLS certificate status

---

## 7️⃣ Monitoring & Observability

### 16. How do Prometheus, Grafana, and Loki work together?
- Prometheus collects metrics
- Grafana visualizes metrics and logs
- Loki stores logs
- Promtail ships logs to Loki  
Grafana provides a single observability dashboard.

---

## 8️⃣ Helm & CI/CD

### 17. What is Helm?
Helm is a package manager for Kubernetes. It packages manifests into charts and supports versioning, upgrades, and rollbacks.

### 18. How do you recover from a failed Helm upgrade?
Check Helm status and history, inspect pod logs and events, fix issues, or rollback:
```bash
helm rollback <release-name>
```

## 19. Explain Jenkins CI/CD flow for EKS (without ArgoCD)
Jenkins automates the deployment of applications to EKS using a CI/CD pipeline.  
The flow is:
- Code checkout from Git repository
- Build and test the application
- Build Docker image
- Push Docker image to Amazon ECR
- Update Kubernetes manifests or Helm values with new image tag
- Deploy to EKS using kubectl or Helm
- Validate deployment using health checks, pod status, or service endpoints

---

## 9️⃣ Common Runtime Issues

### 20. What is ImagePullBackOff?
ImagePullBackOff occurs when Kubernetes cannot pull the container image.  
Common reasons include:
- Incorrect image name or tag
- Missing image in the registry
- No permission to access the registry
- Missing or incorrect imagePullSecrets

---

### 21. ConfigMap vs Secret
ConfigMaps are used to store non-sensitive configuration data like URLs or flags.  
Secrets are used to store sensitive data like passwords, tokens, or keys and should be protected using RBAC and encryption.

---

### 22. Why don’t ConfigMap updates reflect immediately?
Pods do not automatically reload updated ConfigMaps.  
Pods or Deployments must be restarted to pick up the new configuration values.

---

## 🔟 Network Security

### 23. How do you restrict access between namespaces?
Access is restricted using:
- RBAC to control API and resource access
- NetworkPolicies to restrict pod-to-pod network traffic between namespaces

---

### 24. What is a NetworkPolicy?
A NetworkPolicy defines allowed ingress and egress traffic for Pods based on labels, namespaces, and ports.  
It is used to improve security by controlling which Pods can communicate with each other.
