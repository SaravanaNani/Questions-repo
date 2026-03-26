# DevOps Interview Questions

## 🐳 Docker Questions

1. When Jenkins builds a Java app and uses Docker, what exactly happens step-by-step from **Code commit → Docker image → Running container**?

2. ADD vs COPY in Dockerfile?

3. Your Docker image is **2GB**. How do you reduce it?

4. What are **Docker volumes**? Difference between **named volume, bind mount, tmpfs**?

5. A containerized database (MySQL) suddenly loses all data after restart. What could be the possible reasons?

---

## ☸️ Kubernetes Questions

6. You build Docker image and push to ECR. **How does Kubernetes get access to pull image from ECR?**

7. If we rely on **node IAM role for ECR access**, what happens if someone compromises a pod on that node?

8. What is **CrashLoopBackOff**? What are **3 common reasons** it happens? How do you troubleshoot it step-by-step?

9. What is **ImagePullBackOff**? What are possible reasons and how will you troubleshoot?

10. What is the difference between **PersistentVolume (PV), PersistentVolumeClaim (PVC), and StorageClass**?

11. What is the difference between **RWO, ROX, RWX**? Which does **EBS support**?

12. If a pod using **EBS volume** is running on Node A and Node A crashes:
   - What happens to the pod?
   - What happens to the volume?
   - How does Kubernetes recover?

13. How can we **reduce downtime in EBS + node failure scenarios**?

14. Difference between **Deployment vs StatefulSet vs DaemonSet**?

15. Difference between **Labels vs Selectors vs Annotations**?

16. Difference between **Startup Probe vs Liveness Probe vs Readiness Probe**?

17. If your application depends on a **database** and the database goes down temporarily:
   - Which probe should fail? **Liveness or Readiness?**

18. During **rolling update**, how do readiness probes help achieve **zero downtime**?

19. Difference between **NodeSelector vs Node Affinity vs Taints vs Tolerations**?

20. You have **3 worker nodes**.  
   Monitoring pods must run on every node but **application pods should not run on monitoring nodes**.  
   How would you design this using **taints and tolerations**?

21. What are different **Kubernetes Service types**?

22. Why is **NodePort rarely used in production**?

23. Difference between **Ingress vs Service LoadBalancer**?

24. Difference between **ConfigMaps vs Secrets**?

25. What are the **Kubernetes cluster components**?

26. What is **HPA**? What is **VPA**?

27. Difference between **Ingress vs Gateway API vs API Gateway**?

28. A **pod is stuck in Pending state**. How do you troubleshoot?

29. What are **Network Policies**?

30. HPA is **not scaling pods**. What will you check?

31. Difference between **RollingUpdate vs Recreate deployment strategy**?

32. If an **EKS worker node disk becomes full**, what problems can occur?

33. What happens when a **Kubernetes node goes down**? What happens to the pods?

34. Your Kubernetes application **suddenly stops receiving traffic but pods are running**. What will you check?

---

## 🌍 Terraform Questions

35. What is the **Terraform state file**? Why do we use **remote backend**?

36. What problems happen if we **don’t use remote backend**?

37. What is **infrastructure drift**? How does Terraform detect it?

38. Someone manually changed an **EC2 instance type** in AWS.  
What will **terraform plan** show and what will happen on **terraform apply**?

39. What happens if **two developers run terraform apply simultaneously without state locking**?

40. Someone **deleted an EC2 instance manually in AWS** but Terraform state still has it.  
What happens on **terraform plan** and **terraform apply**?

41. What is **Terraform taint**? When would you use it?

42. You accidentally **tainted a production RDS instance**. What will happen and why is it dangerous?

43. Difference between **count vs for_each in Terraform**?

44. What is the **use of S3 and DynamoDB in Terraform backend**?

45. What is the **purpose of lifecycle block in Terraform**?

---

## 🐧 Linux Questions

46. What is the **top command** used for?

47. How do you **find a process using port 8080**?

48. Difference between **Soft link vs Hard link**?

49. A Linux server suddenly becomes **very slow**.  
What **5 commands** will you run first?

50. A service running on **port 8080 stops responding**. How will you troubleshoot?

51. A **log file grows to 100GB** and fills the disk. How will you prevent it permanently?

52. A server suddenly shows **100% CPU usage**. How do you identify the process causing it?

53. A server runs out of **disk space**. What commands will you use to find the cause?

54. Difference between **grep vs egrep vs fgrep**?

55. You started a process in the **foreground accidentally**.  
How do you send it to background and bring it back later?

---

## ⚙️ Jenkins / CI-CD Questions

56. Explain the **complete Jenkins CI/CD pipeline flow for deploying a Java app to Kubernetes**.

57. What are the **typical stages in a Jenkins pipeline** for deploying a Java application?

58. Jenkins pipeline fails during:

kubectl apply -f deployment.yaml
Error: ImagePullBackOff
How will you troubleshoot?

59. Your **Jenkins pipeline fails while pushing image to ECR**. What will you check?

---

## ☁️ AWS Questions

60. What is **IMDS (Instance Metadata Service)**?

61. Difference between **IMDSv1 vs IMDSv2**?

62. Design a **highly available AWS architecture for a web application used by millions of users**.

63. Your **EC2 instances behind Load Balancer become unhealthy**. What will you check?

64. Suppose your **RDS database becomes slow in production**. What will you check?

65. What is **Route53** and how does it work?

66. Auto Scaling **is not launching instances even though CPU usage is high**. What will you check?

67. What happens if **one Availability Zone fails**? How does AWS maintain availability?

68. Difference between **IAM User vs IAM Role vs IAM Policy**?

69. How do **pods access AWS services securely in EKS (IRSA)**?

70. Difference between **ECS Fargate vs ECS EC2**?

71. Difference between **Public Subnet vs Private Subnet**?

72. Difference between **Horizontal Scaling vs Vertical Scaling**?

73. What is the **purpose of a NAT Gateway**?

74. Why do we place **EC2 instances in private subnets**?

75. Difference between **ECS vs EKS**?

76. Difference between **S3 vs EBS vs EFS**?

77. Difference between **Security Group vs Network ACL**?

# DevOps Scenario Interview Questions (Final Set)

## 🔹 Core 10

1. Your Kubernetes application is running, but users cannot access it. How will you debug?

2. A pod is in CrashLoopBackOff. What does it mean and how will you debug it?

3. Your application suddenly becomes very slow. How will you troubleshoot?

4. A pod is stuck in Pending state. What could be the reasons and how will you debug?

5. Your application works internally but is not accessible externally. What will you check?

6. Kubernetes pods are running, but inter-service communication is failing. How will you debug?

7. Your Docker container is running, but the application is not responding. What will you check?

8. EC2 instance CPU is 100% and the application is down. How will you troubleshoot?

9. Jenkins pipeline fails while pushing Docker image to ECR. What will you check?

10. RDS database is slow in production. How will you troubleshoot?

---

## 🔹 Advanced 5 (SRE / Product Level)

11. Application is down but logs show nothing. What will you do?

12. Application works sometimes and fails sometimes (intermittent issue). How will you debug?

13. CPU is normal, but the application is still slow. What will you check?

14. Application broke after a new deployment. How will you handle it?

15. Kubernetes node goes down. What happens and how does the system recover?

---

## 🔹 System Design / Thinking 5

16. App is slow, CPU normal, logs normal — what next?

17. How will you debug a system without access to logs?

18. How do you design zero downtime deployment?

19. How do you handle traffic spikes?

20. How do you debug a memory leak?

---
# DevOps Debugging Cheat Sheet (Focus Points)

## 🔹 Core 10

### 1. App not accessible (pods running)
Focus:
- Service (type, ports)
- Endpoints
- Ingress
- Network (SG/firewall)
- Internal vs external curl

---

### 2. CrashLoopBackOff
Focus:
- Logs
- OOMKilled
- Liveness/Readiness probes
- Config (env, secrets)

---

### 3. App slow
Focus:
- Logs
- DB (slow queries, connections)
- CPU / Memory
- I/O
- Network latency

---

### 4. Pod Pending
Focus:
- FailedScheduling
- Resources (CPU/memory)
- Taints/Tolerations
- Node affinity
- PVC

---

### 5. Internal works, external fails
Focus:
- Service type (NodePort/LB)
- External IP
- Ingress
- Security groups

---

### 6. Inter-service communication fails
Focus:
- DNS (nslookup)
- Service + Endpoints
- curl (connectivity)
- NetworkPolicy
- Port listening

---

### 7. Docker running, app not responding
Focus:
- docker logs
- Process
- netstat (port)
- Port mapping
- Binding (0.0.0.0)

---

### 8. EC2 CPU 100%
Focus:
- top / ps
- Load average
- Identify process
- Logs
- Scale (ASG)

---

### 9. Jenkins → ECR fail
Focus:
- Logs
- IAM permissions
- docker login
- Repo exists
- Network

---

### 10. RDS slow
Focus:
- App logs (slow queries)
- CloudWatch metrics
- Connections
- Locks
- I/O
- Indexing

---

## 🔹 Advanced 5

### 11. No logs, app down
Focus:
- Process
- Port (netstat)
- curl
- Config/startup
- Infra

---

### 12. Intermittent issue
Focus:
- Pattern (time-based)
- Logs
- CPU/memory spikes
- DB / API
- Network

---

### 13. CPU normal, app slow
Focus:
- DB (locks, queries)
- I/O
- Network latency
- Memory
- Thread blocking

---

### 14. After deployment issue
Focus:
- What changed?
- Logs
- Rollback
- Config differences
- Test env

---

### 15. Node failure
Focus:
- Node NotReady
- Pod reschedule
- Stateful vs stateless
- Volume attach

---

## 🔹 System Design 5

### 16. Slow app, CPU/logs normal
Focus:
- DB
- Network
- Thread blocking
- I/O

---

### 17. No logs debugging
Focus:
- Metrics
- Health checks
- curl
- Infra signals

---

### 18. Zero downtime deployment
Focus:
- Rolling update
- Readiness probe
- Load balancer
- Multi-AZ

---

### 19. Traffic spike
Focus:
- Autoscaling (ASG/HPA)
- Load balancer
- Caching
- Rate limiting

---

### 20. Memory leak
Focus:
- Memory increase over time
- OOMKilled
- Logs
- Restart pattern

---

## 🔥 Golden Rule

Logs → Process → Service → Network → Dependency → Infrastructure
## 🔥 Debugging Golden Rule

    Logs → Process → Service → Network → Dependency → Infrastructure
