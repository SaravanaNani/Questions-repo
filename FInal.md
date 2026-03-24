### 1. You said in your resume you used Docker with Jenkins agents. 👉 Explain complete flow:
### When Jenkins builds a Java app and uses Docker, what exactly happens step-by-step from:
   
   Code commit → Docker image → Running container?

    In our setup, when a developer commits code to GitHub, a webhook triggers the Jenkins pipeline.
    
    The pipeline runs inside a Docker-based Jenkins agent. We created a custom Docker image containing Maven, Terraform, AWS CLI, and Java to ensure a consistent build environment across all stages.
    
    In the build stage, Maven compiles and packages the Java application into a JAR/WAR file.
    
    In the next stage, we use a Dockerfile present in the repository to build the application Docker image. The image is tagged using the build number or version and pushed to AWS ECR.
    
    After pushing the image, the deployment stage updates the Kubernetes deployment YAML with the new image tag and applies it using kubectl or Helm.
    
    Kubernetes then pulls the image from ECR and performs a rolling update to replace old pods with new ones.

### 2. You build Docker image and push to ECR. How does Kubernetes get access to pull image from ECR? What authentication mechanism is used?

       In EKS, image pulling is handled by the kubelet on the worker node.
       The worker node IAM role must have permission to access ECR, typically using the AmazonEC2ContainerRegistryReadOnly policy. 
       The kubelet uses this IAM role to authenticate and pull images from ECR.
       
       If they ask about private registry:
       
       Alternatively, imagePullSecrets can be used if using external private registry.

### 3. If we rely on node IAM role for ECR access: What happens if someone compromises a pod on that node? Can they misuse node IAM role?

    Yes, if not properly restricted, a compromised pod may attempt to access the Instance Metadata Service to retrieve temporary credentials of the node IAM role. 
    This is why it's recommended to use IRSA to provide least-privilege access at pod level and restrict IMDS access using IMDSv2 or iptables rules

### 4. What is IMDSv2 and why is it more secure than IMDSv1? What is IMDS?

IMDS = Instance Metadata Service
    
    It is a special AWS internal service available inside EC2.
    
    It allows EC2 instance to:
    
    Get its IAM role credentials
    Get instance info
    Get region info
    
    Accessible at: http://169.254.169.254
    
IMDSv1 vs IMDSv2 (Simple Difference)

    IMDSv1: Simple HTTP request. No token required.Less secure
    IMDSv2: Requires session token first. More secure Prevents SSRF attacks


### 5.What is CrashLoopBackOff? What are 3 common reasons it happens? How do you troubleshoot it step-by-step?

CrashLoopBackOff occurs when a container repeatedly crashes and Kubernetes restarts it with increasing backoff delay.

Common reasons include:

    Misconfigured liveness probe — if the application takes time to start and liveness probe runs too early, Kubernetes may kill a healthy container. This can be solved by configuring a startup probe.
    
    Misconfigured environment variables or incorrect ConfigMap/Secret values causing the application to fail at startup.
    
    Storage-related issues — for example, in our case, Prometheus pod was failing because there was no proper StorageClass and EBS CSI driver configured. After installing the EBS CSI driver and creating the StorageClass, the PVC was bound correctly and the issue was resolved.

    other common reason is OOMKilled when memory limits are too low, causing container restarts.
    
To troubleshoot, I usually:

    Run kubectl describe pod
    
    Check pod events
    
    View container logs using kubectl logs
    
    Verify probe configuration
    
    Validate PVC and storage class if volume is involved

### 6. ImagePullBackOff What are possible reasons and how will you troubleshoot?

ImagePullBackOff occurs when Kubernetes is unable to pull the container image from the registry.

Common reasons include:
    
    Incorrect image name or wrong tag in the deployment YAML.    
    Image does not exist in the registry.
    Authentication or IAM permission issue — for example, worker node IAM role missing ECR read-only permission.
    Registry credentials not configured properly using imagePullSecrets.
    Network connectivity issues between node and registry.

To troubleshoot, I first run: `kubectl describe pod <pod-name>`

and check the Events section for the exact error message. Based on the error, I verify:
    
    Image name and tag
    Registry existence
    Node IAM role permissions
    imagePullSecrets configuration
    Network connectivity if needed.
    
    If company uses private Docker Hub repo:
    Without imagePullSecret:
    → ImagePullBackOff
    With secret:
    → Image pulls successfully
    
    In EKS with ECR:
    Usually node IAM role handles it
    So imagePullSecret is not required.

### 7.What is the difference between: PersistentVolume (PV) PersistentVolumeClaim (PVC) StorageClass

      A PersistentVolume represents actual storage like an AWS EBS volume. 
      
      A PersistentVolumeClaim is a request made by a pod for storage. 
      
      A StorageClass defines how the volume should be dynamically provisioned using a CSI driver. 
      
      In EKS, when a PVC is created, the StorageClass triggers the EBS CSI driver to create an EBS volume, which becomes a PV and gets bound to the PVC. The volume is then attached to the node where the pod runs.
   

### 8. What is difference between RWO, ROX, RWX? Which does EBS support?


Access modes define how a volume can be mounted across nodes. 

      ReadWriteOnce allows a volume to be mounted as read-write by only one node at a time. 
      ReadOnlyMany allows multiple nodes to mount the volume as read-only. 
      ReadWriteMany allows multiple nodes to mount the volume as read-write simultaneously. 
      
      In AWS, EBS supports only ReadWriteOnce because it can be attached to only one EC2 instance at a time.

      Even though EBS supports RWO:
      
      Multiple pods can use same volume if: They are on same node. That’s an advanced nuance.  

### 9. If a pod using EBS volume is running on Node A, and Node A crashes, What happens to: The pod? The volume? How does Kubernetes recover?
      
      If a node running a pod with EBS volume crashes, Kubernetes detects the node as NotReady and reschedules the pod to another node if it's managed by a          Deployment or ReplicaSet.
      However, since EBS supports only ReadWriteOnce, the volume must first detach from the failed node before attaching to the new node. 
      This can cause temporary downtime. If the volume gets stuck in detaching state, manual intervention may be required. 
       In contrast, if the pod uses hostPath, it cannot be rescheduled because the storage is local to the node.

### 10. How can we reduce downtime in such EBS + node failure scenarios?

      I would run multiple replicas across different nodes using Deployments and pod anti-affinity to ensure high availability. 
      I would deploy nodes across multiple availability zones. 
      Since EBS supports only ReadWriteOnce, I would avoid relying on single-replica stateful workloads. For shared storage needs, I would use EFS instead.
      
### 11. What is Terraform state file? Why do we use remote backend? What problems happen if we don’t use remote backend?
      
      Terraform state file stores the mapping between the Terraform configuration and the real-world infrastructure resources. It keeps track of resource IDs, attributes, and dependencies so Terraform knows what to create, update, or delete.
      
      We use a remote backend like S3 to store the state file centrally so multiple team members can collaborate safely. We also enable state locking using DynamoDB to prevent concurrent modifications.
      
      Without remote backend, if developers use local state files, it can lead to conflicts, duplicate resource creation, infrastructure drift, and accidental overwrites.

### 12. What is infrastructure drift? How does Terraform detect it? And how do you handle drift in production?
      
      Infrastructure drift occurs when the actual infrastructure differs from the Terraform state due to manual changes or external modifications. Terraform detects drift during terraform plan, which compares the state file with real infrastructure. If the drifted change is intentional, we can update the Terraform code or import the resource. If the change is unintended, running terraform apply will reconcile the infrastructure back to the desired state.

### 13. Someone manually changed EC2 instance type from t2.micro → t3.medium. What will terraform plan show? And what will happen on terraform apply?

      Terraform plan will show the instance type changing back to t2.micro. 
      On apply, Terraform will either update the instance in-place or replace it depending on AWS behavior.

### 14. What happens if two developers run terraform apply at same time without state locking? What problem can occur?

      If two developers run terraform apply simultaneously without state locking, it can cause state corruption, duplicate resource creation, or inconsistent infrastructure. State locking ensures that only one operation modifies the state at a time, preventing concurrency issues.

### 15. Someone deleted an EC2 instance manually in AWS. Terraform state still has that resource. What happens when you run: terraform plan And what will happen after terraform  apply?

      when we run plan: Terraform checks AWS and sees: EC2 is missing
      So it shows: + aws_instance.my_ec2 will be created
      
      Because Terraform thinks: "Hey, this EC2 should exist but it's missing. I need to create it."
      
      What Happens When You Run terraform apply?
      
      Terraform: Recreates the EC2 instance Updates state file
      
      Now:State = Reality again

### 16. What is Terraform taint? When would you use it?

      Terraform taint marks a resource to be destroyed and recreated during the next apply. It is useful when a resource is in a bad state or misconfigured.
      However, in newer Terraform versions, the recommended approach is using the -replace flag during apply.
      This directly replaces resource without tainting state first.

### 17. You accidentally tainted a production RDS instance. What will happen on next apply? And why can that be dangerous? 
      
      If we taint a production RDS instance, Terraform will destroy and recreate it during the next apply.
      This can cause application downtime and potential data loss if backups or deletion protection are not configured. 
      Therefore, tainting stateful resources in production is extremely risky and should be avoided unless carefully planned.
      For stateful resources like RDS, it is safer to modify parameters in-place or use snapshot-based restoration instead of force replacemen

### 18.Docker Volumes
      
      Named volumes are managed by Docker and used for persistent data storage independent of container lifecycle. 
      Bind mounts map a host directory into a container and are commonly used in development environments. 
      tmpfs mounts store data in memory and are used for temporary or sensitive data that should not be written to disk.
      
### 19. A containerized database (MySQL) suddenly loses all data after restart. What could be the possible reasons?

      The most common reason is that the MySQL container was running without a persistent volume, so data was stored inside the container filesystem and lost after restart. 
      Other reasons include incorrect volume mount path, recreating container without reusing the same named volume, or accidental deletion of bind-mounted host directories.
      
### 20. ADD vs COPY in dockerfile?

      COPY and ADD both copy files into the image. However, ADD has additional features like downloading files from remote URLs and automatically extracting tar archives. Because ADD has implicit behavior and less predictability, it is recommended to use COPY for simple file transfers and use RUN with curl or wget for downloading files.

### 21. Your Docker image is 2GB. How do you reduce it?

      First, I would check the base image. Instead of using a full OS image like Ubuntu, I would switch to a lightweight base image like Alpine or a slim variant.
      
      Second, I would implement multi-stage builds. In the first stage, I build the application with all dependencies. In the final stage, I copy only the required artifact, such as a JAR or WAR file, into a minimal runtime image.
      
      Third, I would use a .dockerignore file to exclude unnecessary files like .git, logs, node_modules, or build artifacts from the build context.
      
      Fourth, I would optimize layer caching by copying dependency files first and running dependency installation before copying the full source code.
      
      Finally, I would clean up unnecessary packages and remove cache files during build to reduce layer

      Remove build tools from runtime image

     Use distroless images for production

### 22. Deployment vs Statefulset vs Deamonset
      
      Deployment is used for stateless applications and manages replica count with rolling updates and rollback support. StatefulSet is used for stateful applications that require stable network identity and persistent storage. DaemonSet ensures that a specific pod runs on every node, commonly used for monitoring or logging agents.

### 23 labels vs selectors vs Annotations

      Labels are key-value pairs used to identify and group Kubernetes objects and are used for selection by services and replica sets. 
      Selectors use labels to match specific objects. 
      Annotations are also key-value pairs but are used to store additional metadata and are not used for selection.

### 24. Startup vs liveness  vs Readiness Probes

      Startup probe is used for slow-starting applications and ensures liveness and readiness probes do not run until the application is fully initialized.
      Liveness probe checks whether the application is healthy and restarts the container if it fails. 
      Readiness probe determines whether the pod is ready to receive traffic and removes it from service endpoints if it fails.

### 25. Your application depends on a database. The database goes down temporarily. Which probe should fail? Liveness or Readiness? And why?

      If the database goes down temporarily, the readiness probe should fail so the pod is removed from service endpoints and stops receiving traffic. 
      Liveness probe should not fail because restarting the container will not resolve an external dependency issue.

### 26. During rolling update: How do readiness probes help achieve zero downtime?
      
      During a rolling update, Kubernetes creates new pods first. The readiness probe ensures that new pods do not receive traffic until they are fully ready.
      Once the readiness probe succeeds, the pod is added to service endpoints.
      Only then are old pods terminated. This controlled traffic shifting ensures zero downtime.

Important Detail You Slightly Missed Rolling update behavior is controlled by:

      strategy:
      rollingUpdate:
       maxUnavailable
       maxSurge
      
      Example:
      
      maxUnavailable: 1
      maxSurge: 1
      
      This ensures at least minimum pods are always running.

      maxUnavailable defines how many pods can be unavailable during a rolling update, 
      while maxSurge defines how many extra pods can be temporarily created above the desired replica count.
      Together they control availability and update speed during deployment rollout.

### 27.  Node elector vs node affintiy vs taints vs tolerations

      NodeSelector and Node Affinity are used to control pod placement based on node labels.
      Pod Affinity and Anti-Affinity control pod-to-pod placement rules. 
      Taints are applied to nodes to prevent pods from being scheduled, and tolerations allow specific pods to bypass those restrictions.

### 28.You have 3 worker nodes. You want: Monitoring pods to run on every node Application pods NOT to run on monitoring-only node. How would you design this using taints and tolerations?

      Monitoring pods are deployed using DaemonSet so they run on every node. If a node should be reserved only for monitoring workloads, we can apply a taint to that node. 
      Monitoring pods will have a matching toleration, allowing them to run there, while application pods without tolerations will not be scheduled on that node.

### 29. Servcies in k8s

      Kubernetes provides different service types such as ClusterIP, NodePort, and LoadBalancer. 
      ClusterIP is the default service used for internal communication within the cluster. 
      NodePort exposes the service on a specific port of each node. 
      LoadBalancer creates a cloud load balancer to expose the application externally. 
      Headless services are used mainly for StatefulSets to provide direct DNS access to individual pods. 
      ExternalName services map a Kubernetes service to an external DNS name.
 
 ### 30.  why nodeport serivce rarely used in Prod?
       
      NodePort exposes services on every worker node, which can create security risks and is difficult to manage at scale.
      It also does not provide a managed external load balancer. 
      In production environments, we typically use LoadBalancer services or an Ingress controller to expose applications more securely and efficiently.

### 31. Ingress vs Service LB?
      
      A Service of type LoadBalancer exposes a service externally by creating a cloud load balancer and works at Layer 4 with basic routing. Ingress, 
      on the other hand, is a Layer 7 resource that provides advanced routing like host-based and path-based routing
      using a single entry point, making it more suitable for production environments.

Ingress is a Kubernetes object that manages external HTTP/HTTPS access to services inside the cluster.
      
      Host-based routing
      Path-based routing
      SSL termination
      But important:
      
      👉 Ingress itself does not handle traffic.
      👉 It needs an Ingress Controller (like NGINX, AWS ALB, Traefik).

      

### 32. ConfigMAps vs Secrets

      ConfigMaps store non-sensitive configuration data such as application settings, while Secrets store sensitive information like passwords or tokens. 
      Both can be injected into pods using environment variables or mounted as volumes, but Secrets are base64 encoded and handled more securely by Kubernetes.

### 33. K8s Cluster components

      The Kubernetes control plane consists of the API server, scheduler, etcd, and controller manager. The API server is the entry point for all cluster communication. etcd stores the cluster state. The scheduler assigns pods to nodes based on resource availability and constraints. The controller manager ensures the desired state of the cluster is maintained. On worker nodes, kubelet manages pod execution, kube-proxy handles networking, and the container runtime runs containers.
 
 ### 34. HPA? VPA

      HPA automatically scales pods based on metrics like CPU, memory, or custom metrics. 
      It requires metrics-server to collect resource usage from kubelets.
      The HPA controller then compares the metrics with the configured threshold and increases or decreases the number of pod replicas accordingly. 
      For event-driven scaling, tools like KEDA can be used.
      
      HPA scales the number of pods based on metrics like CPU or memory. 
      
      VPA adjusts the resource requests and limits of containers based on usage patterns. 
      VPA typically requires restarting pods to apply new resource values.  
      
### 35. INGRESS VS GaTEWAY API VS API GATEWAY

IN Ingress the Infra team + App routing often managed together in the same resource.

Responsibilities: Host-based routing - Path-based routing - SSL termination

Reverse proxy: Gateway API is the next-generation replacement for Ingress.

It separates responsibilities between teams.

Infrastructure Team

      Manages:
      GatewayClass
      Gateway
      
      Responsibilities: Load balancer -TLS configuration - Network infrastructure

Application Team

      Manages:
      HTTPRoute
      TCPRoute
      GRPCRoute
      
      Responsibilities: Service routing - Path rules - Backend services
      
API Gateway

      API Gateway is NOT a Kubernetes resource.
      
      It is an API management layer.
      eg:
      AWS API Gateway
      Istio Gateway
      
      Responsibilities: Authentication - Rate limiting - API versioning -Request transformation - Analytics

### 36. Pod PEnding ?

      When a pod is in Pending state, I first check the pod events using kubectl describe pod. 
      Then I verify node resources like CPU and memory availability, check node selectors or affinity rules, verify taints and tolerations, and ensure persistent volume claims are successfully bound. Finally, I confirm that nodes are in Ready state and available for scheduling.

### 37. Network Policies?

      NetworkPolicy is used to control network traffic between pods or namespaces in a Kubernetes cluster. 
      It works like a firewall that defines which pods can communicate with other pods or external services.
      It is commonly used to isolate workloads and improve security in multi-tenant clusters.

### 38. HAP Doesn;t scale the Pods?
      
      If HPA is not scaling, I check the HPA status, verify metrics-server is running, inspect HPA events, 
      confirm CPU requests are defined in the pod spec, and check whether the cluster has enough node capacity to schedule new pods.
### 39. Rolling Update VS Recreate Deployment Statergy
      
      RollingUpdate updates pods gradually by replacing old pods with new ones without bringing down the entire application, ensuring zero downtime. 
      Recreate strategy deletes all existing pods first and then creates new pods, which can cause temporary downtime but is useful when applications cannot run multiple versions simultaneously.

 ### 40. count vs for_each in Terraform?
   
    count is used to create multiple identical resources using numeric indexing,
    while for_each is used when resources need unique identifiers and works with maps or sets. 
    for_each is generally preferred when managing resources with unique names because it provides stable resource addressing.
    
### 41. USe case of s3 and dyanmodb table in terraform?

      S3 backend is used to store the Terraform state file remotely so that all team members can share a centralized state. 
      DynamoDB is used for state locking to prevent multiple users from modifying the state file simultaneously, 
      ensuring safe and consistent infrastructure changes.

### 42. Purpose of LifeCycle Block in Terraform - pRevent to destory and create before destory and ingnore changes

The lifecycle block in Terraform controls how resources are created, updated, or destroyed.

      prevent_destroy protects critical resources from accidental deletion, 
      
      create_before_destroy ensures new resources are created before old ones are removed to reduce downtime, and 
      
      ignore_changes allows Terraform to ignore specific attribute changes made outside Terraform.
      
### 43 . In Jenkins pipeline, what are the typical stages you implement for deploying a Java application to Kubernetes?

Our Jenkins pipeline includes stages such as
   
         code checkout, 
         application build using Maven, 
         code quality analysis using SonarQube, 
         container security scanning using Trivy,
         Docker image build and push to AWS ECR,and 
         finally deployment to Kubernetes using kubectl. 
      
      The pipeline runs on a Docker-based Jenkins agent to maintain a consistent build environment.

### 44.  During deployment, Jenkins pipeline fails at this stage: kubectl apply -f deployment.yaml. Error:ImagePullBackOff    
      
      If I encounter an ImagePullBackOff error, 
      I first check the pod events using kubectl describe pod to identify the exact issue. 
      Then I verify the image name and tag in the deployment manifest, ensure the image was successfully pushed to the container registry, and confirm that the Kubernetes cluster has proper authentication to pull the image, such as imagePullSecrets or IAM permissions in EKS.
      The Worker node has NAT to fetch the regirsty   

 ### 45. If one pod keeps restarting while others are healthy?

      If one pod keeps restarting while others are healthy, I first check the pod logs and events to see if the application is crashing or if the liveness probe is failing. Then I verify resource limits to check for OOMKilled errors. If those are fine, I investigate node-level issues like memory pressure or disk pressure. I also check if any volume mounts or dependencies like databases are causing the container to crash.

### 46 use of Top comand in linux?

      The top command is used to monitor real-time system performance including
      CPU usage, memory consumption, running processes, load average, and process IDs.

### 47. Find process using port 8080
 
       netstat -tulnp | grep 8080
       
       ss -tulnp | grep 8080
       
       lsof -i :8080 - this shows process ID that uses the port 8080

### 48. Soft link vs Hard link

      A hard link is another name for the same file inode and cannot span different file systems,
      while a soft link (symbolic link) is a pointer to the file path and can link across file systems.
      A hard link remains valid if the original file is deleted, but a soft link breaks if the target is removed.

### 49.  A Linux server suddenly becomes very slow. What 5 commands will you run first to diagnose the issue? Explain what each command checks.

If a Linux server becomes slow, 
      
      I first check system resource usage using commands like top or htop to identify CPU-intensive processes.
      
      Then I check system load using uptime, 
      
      verify memory usage using free -m, 
      
      check disk space using df -h, 
      
      and analyze disk I/O using iostat or iotop to identify bottlenecks.

### 50. A service running on port 8080 suddenly stops responding. What steps will you take to troubleshoot it?    

   If a service on port 8080 stops responding, 
   
      I first check whether the process is running using commands like ps or lsof. 
      
      Then I verify if the port is listening using netstat or ss. 
      
      If the service is running inside a container, I inspect the container status and logs. 
      
      I also check application logs for errors - journalctl -f servicename and 
      
      verify firewall rules or resource issues that may prevent the service from responding.
      
### 51. A log file on a Linux server grows to 100 GB and fills the disk. How would you prevent this problem permanently?
      
      To prevent large log files from filling the disk, I would configure log rotation using the logrotate utility. 
      It automatically rotates, compresses, and deletes old logs based on defined retention policies.
      In production environments, logs are also usually shipped to centralized logging systems like ELK or CloudWatch to avoid disk space issues.

 ### 52. A server suddenly shows 100% CPU usage. How will you identify which process is causing it and stop it if necessary? 

      If CPU usage reaches 100%, 
      
      I first run top or htop to identify the process consuming high CPU.
      
      Then I confirm the process details using ps. 
      
      If it is an abnormal or stuck process, I try to terminate it gracefully using kill, and if necessary use kill -9.
      
      After that, I investigate the application logs or cron jobs to identify the root cause.

### 53. A server suddenly runs out of disk space. What commands will you run to find what is consuming the space?

If a server runs out of disk space, 
      
      I first run df -h to identify which filesystem is full. 
      
      Then I use du -sh to find directories consuming the most space. 
      
      I drill down into large directories and use find to identify large files. 
      
      After identifying the root cause, I clean up unnecessary logs, temporary files, or rotate logs to free space.

### 54. What is the difference between: grep vs egrep vs fgrep?

      grep is used to search for patterns in files or command output.
      egrep supports extended regular expressions for more complex pattern matching.
      fgrep searches for fixed strings without interpreting regex patterns and is faster for large files.

 ### 55. You are SSH’d into a Linux server and accidentally started a process in the foreground. Example: python script.py Now the process is running and blocking your terminal.
How do you:
1️⃣ Send it to background
2️⃣ Bring it back to foreground later?
   
    If a process is running in the foreground and blocking the terminal,
    
    I press Ctrl+Z to suspend it and then run the " bg "command to send it to the background. 
    
    Later, I can bring it back to the foreground using the " fg " command.
    
    I can also check running background jobs using the " jobs " command.

### 56. You have a web application used by millions of users. Design a highly available AWS architecture.

To design a highly available AWS architecture,

      I would create a VPC across multiple Availability Zones with public and private subnets. 
      
      An Application Load Balancer in the public subnet distributes incoming traffic to EC2 instances running in an Auto Scaling group in private subnets.
      
      The database layer uses Amazon RDS with Multi-AZ deployment for high availability. 
      
      A NAT Gateway allows private instances to access the internet securely.
      
      For performance improvement, I would integrate ElastiCache Redis. Security is enforced using security groups and IAM roles.

### 57. Your EC2 instances behind the Load Balancer suddenly become unhealthy. What are the first 5 things you will check to troubleshoot this? Answer step-by-step like production troubleshooting.

      If an EC2 instance behind a load balancer becomes unhealthy,
      I first check the target group health status to see the exact failure reason.
      Then I verify the health check configuration such as path and port. 
      Next, I confirm the EC2 security group allows traffic from the load balancer. 
      I also log into the instance to check whether the application service is running and verify system resources like CPU and memory.
      Finally, I check CloudWatch metrics to see if resource exhaustion or traffic spikes caused the issue.

### 58. Suppose your RDS database suddenly becomes slow in production. What 5 things will you check immediately?

      If an RDS database becomes slow, I first check CloudWatch metrics such as CPU utilization, database connections, memory usage, and IOPS.
      Then I review slow query logs to identify inefficient queries.
      I also verify whether the instance has reached connection limits or storage I/O limits.
      If required, I scale the instance or optimize queries and indexes

### 59. If your EKS worker node disk becomes full, what problems can occur?

If node disk becomes full:

Problems may occur:

      1️⃣ Pods fail to start
      2️⃣ Container image pull fails
      3️⃣ kubelet cannot write logs
      4️⃣ Pods enter Evicted state      

### 60. Your website suddenly becomes slow in production. What 3 layers will you check first? 
Example: infrastructure - application - database
Explain your troubleshooting approach.


Check three layers.

1️⃣ Infrastructure layer
      
      Check: CPU - Memory - Disk - Network
      Commands:
            top
            free -m
            df -h

2️⃣ Application layer

      Check: application logs - thread pool - API latency
      
      Example:
      500 errors
      timeouts

3️⃣ Database layer
      
      Check: slow queries - DB connections - IOPS
      
      Example:
      CPU 95%
      slow query logs


### 61. What happens when a Kubernetes node goes down? What happens to the pods?

When a node becomes NotReady, Kubernetes detects it using the Node Controller.

FOr stateless pods: New pods scheduled on healthy nodes

      Node failure
       ↓
      Node marked NotReady
       ↓
      Pods on that node become unavailable
       ↓
      Scheduler reschedules pods on another node

For Statefulset pods: 

      If using EBS/PVC → pod rescheduled and volume reattached
      
      If using local storage → pod may wait until node recovers

### 62. Your Jenkins pipeline suddenly fails when pushing image to ECR. WHat you will check
      
      1️⃣ Check AWS login : aws ecr get-login-password
      
      2️⃣ Check IAM permissions Required policy: AmazonEC2ContainerRegistryFullAccess
      
      3️⃣ Check ECR repository exists : aws ecr describe-repositories
      
      4️⃣ Check Docker login: docker login
      
      5️⃣ Check image tag format: Example: 123456.dkr.ecr.us-east-1.amazonaws.com/app:v1


### 63 What is Route 53 and how does it work?

Route 53 is AWS DNS service.

      It maps: domain name → IP address
      
      Example: example.com → 52.12.45.21
      
      It supports routing strategies:
      
      Simple routing
      Weighted routing
      Latency routing
      Failover routing
      Geo-location routing
      
      Example failover: Primary server fails → traffic goes to backup server

### 64. Auto Scaling not launching instances even though CPU usage is high. What will you check?
      
      1️⃣ Check scaling policy : CPU threshold configured?
      
      2️⃣ Check Auto Scaling limits: min - max - desired
      
      Example problem: max instances = 2 -> Scaling cannot happen.
      
      3️⃣ Check launch template configuration
      
      Possible problems: wrong AMI - invalid instance type - missing IAM role
      
      4️⃣ Check subnet capacity: Subnet may not have enough IP addresses.
      
      5️⃣ Check CloudWatch alarms : Scaling depends on CloudWatch metrics.
      
### 65. What happens if one Availability Zone fails? How does AWS maintain availability?

      AWS designs architecture across multiple AZs.
      Load balancer distributes traffic across AZs.
      
            If AZ-1 fails:
      
            Load Balancer
               ↓
            Only AZ-2 servers receive traffic
      
      Also:
      Auto Scaling launches replacement instances
      RDS Multi-AZ switches to standby database

### 66. IAM User vs Role vs Policy

      | Component  | Purpose                                   |
      | ---------- | ----------------------------------------- |
      | IAM User   | identity for a person or application      |
      | IAM Role   | temporary permissions assumed by services |
      | IAM Policy | permission rules defining allowed actions |


### 67. How Pods Access AWS Services Securely (IRSA)
### 68. ECS Fargate vs ECS EC2
### 69. Public subent vs private subnet
### 70. Horizontal vs Vertical Scaling
### 71. What is the purpose of a NAT Gateway?

### 72. Why EC2 in Private Subnet?
      EC2 instances are placed in private subnets to prevent direct internet access.
      Benefits: better security - controlled access - protection from direct attacks

### 73. ECS vs EKS?
      
      | Feature    | ECS                   | EKS                   |
      | ---------- | --------------------- | --------------------- |
      | Type       | AWS container service | Managed Kubernetes    |
      | Complexity | Simple                | Advanced              |
      | Control    | AWS managed           | Kubernetes control    |
      | Use case   | small/simple apps     | complex microservices |

### 74. S3 vs EBS vs EFS
### 75. Security Group vs Network ACL
---
### 76.Your Kubernetes application suddenly stops receiving traffic, but pods are running. What are the first things you will check?

      If pods are running but traffic is not reaching the application,
      I first check pod readiness status. Then I verify the Kubernetes service configuration and ensure the service selectors match the pod labels. 
      Next I check service endpoints to confirm the pods are registered. 
      After that I validate ingress rules and ensure the ingress controller is running properly. 
      Finally I check the load balancer, security groups, and DNS configuration to ensure traffic is reaching the cluster
---
### 77. Explain a production incident you handled and how you resolved it.”

Situation: Our Jenkins CI/CD server suddenly became unavailable.
      
    Problem: Developers could not trigger builds.
      
    First I checked: EC2 instance status and Jenkins service: systemctl status jenkins
      
    The service was corrupted due to configuration issues.
      
    Root Cause: Jenkins configuration files were corrupted.
      
    Fix: We restored Jenkins using Latest EC2 snapshot. Restored Jenkins configuration from /var/lib/jenkins
      
    After restoring the directory: Jenkins restarted successfully
      
Result: CI/CD pipelines resumed without losing job configurations.
---
Situation : While deploying the Prometheus monitoring stack in our Kubernetes cluster, the Prometheus pod was stuck in Pending state.

      Problem: The pod could not start because the PersistentVolumeClaim was not getting bound.
      
      First I checked: kubectl describe pod prometheus | kubectl get pvc
      
      I noticed: PVC status: Pending
      
      Then I checked whether a StorageClass was available: kubectl get storageclass
      
      There was no storage class configured.
      
      Root Cause: The cluster did not have the AWS EBS CSI driver installed, so dynamic volume provisioning was not working.
      
      Fix Steps taken: Installed AWS EBS CSI driver and Created a StorageClass
      
      Prometheus PVC automatically created the EBS volume
      
      After that: PVC → Bound -> Pod → Running

Result: Prometheus started successfully and monitoring stack became operational.
