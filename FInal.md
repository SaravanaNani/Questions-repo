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

    A PersistentVolume (PV) is a cluster-scoped storage resource provisioned either manually or dynamically. 
    It represents actual storage like an EBS volume.
    
    A PersistentVolumeClaim (PVC) is a request made by a pod for storage with specific size and access mode.
    
    A StorageClass defines how dynamic provisioning should happen. It specifies the provisioner (like EBS CSI driver), volume type, and parameters.
    
    In EKS, when a pod creates a PVC, Kubernetes checks the StorageClass. The StorageClass triggers the EBS CSI driver, which creates an EBS volume in AWS. 
    Once created, it becomes a PV and is bound to the PVC. The volume is then attached to the worker node where the pod is running.
   ### 8.  
