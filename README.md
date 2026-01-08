# Questions-repo
    Hi, my name is Saravana. I have around 2 years of experience as a DevOps Engineer.
    I’ve worked mainly on AWS, Terraform, Jenkins, Docker, Ansible, and Kubernetes.
    
    In my first project, I automated AWS infrastructure using Terraform and supported CI/CD pipelines for Java applications using Jenkins,           Maven, and Docker.
    
    In my second project, I supported an AWS EKS platform where I worked on ingress, TLS, monitoring using Prometheus and Grafana, and               troubleshooting pod and storage issues.

I enjoy working on automation, monitoring, and production support, and I’m now looking for a role where I can grow further in cloud and DevOps technologies.”

# Can you briefly explain your experience and what you worked on in your last role
I have worked on both monolithic and microservices-based environments.
    
In my first project, I worked mainly on AWS infrastructure automation using Terraform modules. I provisioned services like VPC, EC2, S3, and IAM, and implemented remote backend using S3 and DynamoDB. I also built CI/CD pipelines in Jenkins for Java application deployments and created Docker-based Jenkins agents to maintain a consistent build environment. I assisted in troubleshooting build failures and deployment issues using logs and Bash scripts.
    
In my second project, I supported an AWS EKS platform along with a senior engineer. My responsibilities included validating ingress and TLS configuration, setting up centralized monitoring using Prometheus, Grafana, and Loki, and ensuring workloads were running properly. I also helped troubleshoot issues like pod CrashLoopBackOff, PVC pending states, and storage-related problems.
    
Overall, my experience includes infrastructure automation, CI/CD, Kubernetes monitoring, and production issue troubleshooting. I’m now looking for a role where I can further grow and work more deeply with cloud and DevOps tools in a production environment.”

# What is Major Issues you Gone through in Production

“One major issue we faced in production was Prometheus pods going into PVC Pending state. Prometheus is stateful and requires persistent storage, but dynamic volume provisioning was not configured in the EKS cluster. After identifying the root cause, we created a StorageClass, installed the AWS EBS CSI Driver, and configured IRSA so the CSI driver could securely provision EBS volumes. Once this was done, PVCs were created automatically and Prometheus became stable.

Another issue we faced was a Jenkins server crash. We recovered the server by creating a new EC2 instance from the latest snapshot and restoring the Jenkins configuration directory. After recovery, we reconfigured access securely by recreating users, restricting network access using security groups, and ensuring only authorized IPs could access Jenkins. This helped us bring the CI/CD system back online with minimal downtime.”

# How do you ensure security in your AWS and DevOps environment?

    We secure our infrastructure by following the principle of least privilege, 
    where each AWS service and user has only the required IAM permissions. 
    We restrict network access using security groups with minimal inbound rules and place application servers in private subnets.
    
    We scan Docker images for vulnerabilities before deployment and use Docker-based pipelines to maintain consistent build and deployment           environments. 
    Sensitive data like passwords and tokens are stored securely using Jenkins credentials or Vault instead of hardcoding them.
    
    Additionally, we enable logging and monitoring using CloudWatch and alerts to quickly detect any suspicious activity.

# Explain a simple CI/CD pipeline you have worked on end to end.

    “I worked on a scripted Jenkins CI/CD pipeline for Java application deployment. 
    The pipeline starts with code checkout from Git, followed by SonarQube analysis for code quality checks.
    
    Next, the application is built using Maven, and the artifact version is updated in the pom.xml using the Jenkins build number. 
    After a manual approval, the artifact is uploaded to Nexus.
    
    For deployment, the same artifact is pulled from Nexus to ensure build-once and deploy-many.
    After deployment, we run validation checks to confirm the application is up and accessible.
    Finally, email notifications are sent to inform the team about the pipeline status.”

# How do you monitor applications and infrastructure in your projects?

    In my first project, we used AWS CloudWatch to monitor EC2 instances, application logs, and system metrics like CPU, memory, and disk usage.     We configured CloudWatch alarms to notify the team when thresholds were breached.
    
    In my second project on EKS, we implemented centralized monitoring. We used Prometheus to scrape metrics from Kubernetes workloads, Promtail      to collect application logs, and Loki as a centralized log store. Grafana was used to visualize metrics and query logs.
    
    We also configured alerts in Grafana so issues like pod crashes or high resource usage could be detected early and resolved quickly.”

# What happens when an application deployed on Kubernetes is not accessible, and how do you troubleshoot it?

    “First, I check the pod and deployment status to ensure the application is running. 
    I verify pod logs and events using kubectl logs and kubectl describe pod.
    
    Next, I check the Service to confirm endpoints are created and correctly mapped to the pods.
    Then I verify the Ingress controller is running and validate the Ingress configuration, including paths, host rules, and TLS settings.
    After that, I check DNS routing, such as CNAME records in the domain provider, to ensure traffic is pointing to the load balancer.
    Finally, I review networking components like security groups, node networking, and cluster-level networking if the issue persists.”

# A Pod is stuck in CrashLoopBackOff. How do you troubleshoot it?

When a pod goes into CrashLoopBackOff, I first describe the pod and check events to see why it is restarting. 
Then I check the container logs to identify application startup or configuration issues.

Common causes I check are:
    
    Application startup failures – wrong command, missing dependency, or app crashing on boot.    
    Probe issues – misconfigured liveness or readiness probes causing repeated restarts.
    Port conflicts – hostPort already in use, so I change the port or avoid hostPort.
    Configuration or Secret mismatch – missing or incorrect keys in ConfigMaps or Secrets.
    PVC issues – volume not mounted or PVC stuck in Pending state.
    Resource limits – container hitting memory limits and getting OOMKilled.
    After identifying the root cause, I fix the configuration or resource issue and redeploy the pod.”

# A PVC is stuck in Pending state in EKS. How do you troubleshoot and fix it?

    “First, I check the PVC events to understand why it is in Pending state.
    Then I verify whether a StorageClass exists and whether it is set as default or referenced correctly in the PVC.
    
    Next, I check if the AWS EBS CSI Driver is installed and running in the cluster.
    I also verify IRSA permissions to ensure the CSI driver has access to create EBS volumes.
    
    Finally, I confirm that the pod and node are in the same Availability Zone as the EBS volume, because EBS volumes are AZ-specific.”

# How do you troubleshoot an application that is running, but not accessible from the browser?

    If it is a monolithic setup on a VM, I first check application and system logs using tools like journalctl -f or application logs to confirm     the service is running and listening on the expected port.
    
    In a microservices or Kubernetes setup, I follow a layered approach.
    First, I check whether the pods are running. If not, 
    I describe the pod and check logs to identify issues like crashes or configuration errors.
    
    Next, I verify the Service and ensure the endpoints are correctly mapped to the pods using labels.
    
    Then, I check whether the Ingress controller is running and validate the Ingress rules, paths, and TLS configuration.
    
    After that, I verify DNS mapping at the domain provider level and ensure traffic is pointing to the correct LoadBalancer.
    Finally, I check security groups and network rules to confirm the required ports are open.

# A Kubernetes Service exists, but traffic is not reaching the pods. How do you troubleshoot it?”

    If a Kubernetes Service exists but traffic is not reaching the pods, I troubleshoot step by step.
    
    First, I check whether the pods are running and ready.
    Then I verify that the Service selector matches the pod labels, because mismatched labels will result in no endpoints.
    
    Next, I check the Service endpoints to confirm pods are registered.
    If endpoints are missing, it means the Service is not routing traffic to any pod.
    
    After that, I verify the Service type and port mappings (targetPort vs containerPort).
    
    Then I check whether the Ingress controller is running and validate the Ingress rules and paths.
    
    Finally, I verify DNS resolution and security group rules to ensure traffic is allowed to reach the cluster.”

# How do you deploy a new version of an application in Kubernetes without downtime?

    “To deploy a new version without downtime, I use Kubernetes rolling updates.
    
    In our setup, Jenkins builds a new Docker image and pushes it to the registry.
    Jenkins then updates the image tag in the Kubernetes manifest or Helm values file and applies it using kubectl or Helm.
    
    Kubernetes creates new pods gradually while keeping old pods running until the new pods become ready.
    Traffic is shifted automatically through the Service, ensuring zero downtime.
    
    If any issue occurs, I rollback using kubectl rollout undo deployment <name>.”
   
    Old pods → still serving traffic
    New pods → start with the new image
    Once new pods are Ready, old pods are removed
    Users don’t see downtime
    
    We control this behavior using rolling update settings like maxUnavailable and maxSurge.

    strategy:
    type: RollingUpdate
    rollingUpdate:
      maxUnavailable: 0
      maxSurge: 1

    ⭐ Bonus line (very good impression)
    
    “Readiness probes ensure traffic is sent only to healthy pods during rollout.”

# Pods are stuck in Pending state. What are the possible reasons and how do you debug it? 
    
       “If pods are stuck in Pending state, I first describe the pod to check scheduler events.
    I verify node resource availability like CPU and memory.
    I then check for node taints, tolerations, and node affinity rules that may prevent scheduling.
    I also verify PVC status, storage availability, and image-related issues if required.”

# ConfigMap was updated but the application didn’t reflect the changes. Why, and how do you fix it? 
    
    “If ConfigMap is mounted as a file, Kubernetes updates the file, but most applications still require a restart to re-read the configuration.”
    ConfigMap changes do not reflect automatically because pods do not reload configuration at runtime.
    To apply the changes, I restart the pod or roll the deployment so the new ConfigMap values are loaded when the container starts.”
# How do you manage secrets securely in Kubernetes?

    “We manage secrets securely using Kubernetes Secrets or external secret managers like AWS Secrets Manager or HashiCorp Vault.
    Secrets are never hardcoded in code or Git. They are mounted into pods as environment variables or files, and access is controlled using RBAC and IAM roles like IRSA.”

# “A Kubernetes node goes down. What happens to the pods running on that node?”
    “When a Kubernetes node goes down, the control plane marks the node as NotReady.
    The pods running on that node are terminated, and Kubernetes automatically reschedules them on healthy nodes to maintain the desired state,      as long as they are managed by a controller like a Deployment or ReplicaSet.”
