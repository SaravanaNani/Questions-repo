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
