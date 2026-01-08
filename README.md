# Questions-repo
# Can you briefly explain your experience and what you worked on in your last role
I have worked on both monolithic and microservices-based environments.
    
In my first project, I worked mainly on AWS infrastructure automation using Terraform modules. I provisioned services like VPC, EC2, S3, and IAM, and implemented remote backend using S3 and DynamoDB. I also built CI/CD pipelines in Jenkins for Java application deployments and created Docker-based Jenkins agents to maintain a consistent build environment. I assisted in troubleshooting build failures and deployment issues using logs and Bash scripts.
    
In my second project, I supported an AWS EKS platform along with a senior engineer. My responsibilities included validating ingress and TLS configuration, setting up centralized monitoring using Prometheus, Grafana, and Loki, and ensuring workloads were running properly. I also helped troubleshoot issues like pod CrashLoopBackOff, PVC pending states, and storage-related problems.
    
Overall, my experience includes infrastructure automation, CI/CD, Kubernetes monitoring, and production issue troubleshooting. I’m now looking for a role where I can further grow and work more deeply with cloud and DevOps tools in a production environment.”

# What is Major Issues you Gone through in Production

“One major issue we faced in production was Prometheus pods going into PVC Pending state. Prometheus is stateful and requires persistent storage, but dynamic volume provisioning was not configured in the EKS cluster. After identifying the root cause, we created a StorageClass, installed the AWS EBS CSI Driver, and configured IRSA so the CSI driver could securely provision EBS volumes. Once this was done, PVCs were created automatically and Prometheus became stable.

Another issue we faced was a Jenkins server crash. We recovered the server by creating a new EC2 instance from the latest snapshot and restoring the Jenkins configuration directory. After recovery, we reconfigured access securely by recreating users, restricting network access using security groups, and ensuring only authorized IPs could access Jenkins. This helped us bring the CI/CD system back online with minimal downtime.”

# How do you ensure security in your AWS and DevOps environment?

“We secure our infrastructure by following the principle of least privilege, where each AWS service and user has only the required IAM permissions. We restrict network access using security groups with minimal inbound rules and place application servers in private subnets.

We scan Docker images for vulnerabilities before deployment and use Docker-based pipelines to maintain consistent build and deployment environments. Sensitive data like passwords and tokens are stored securely using Jenkins credentials or Vault instead of hardcoding them.

Additionally, we enable logging and monitoring using CloudWatch and alerts to quickly detect any suspicious activity.”

# Explain a simple CI/CD pipeline you have worked on end to end.

“I worked on a scripted Jenkins CI/CD pipeline for Java application deployment. The pipeline starts with code checkout from Git, followed by SonarQube analysis for code quality checks.

Next, the application is built using Maven, and the artifact version is updated in the pom.xml using the Jenkins build number. After a manual approval, the artifact is uploaded to Nexus.

For deployment, the same artifact is pulled from Nexus to ensure build-once and deploy-many. After deployment, we run validation checks to confirm the application is up and accessible. Finally, email notifications are sent to inform the team about the pipeline status.”

# How do you monitor applications and infrastructure in your projects?

In my first project, we used AWS CloudWatch to monitor EC2 instances, application logs, and system metrics like CPU, memory, and disk usage. We configured CloudWatch alarms to notify the team when thresholds were breached.

In my second project on EKS, we implemented centralized monitoring. We used Prometheus to scrape metrics from Kubernetes workloads, Promtail to collect application logs, and Loki as a centralized log store. Grafana was used to visualize metrics and query logs.

We also configured alerts in Grafana so issues like pod crashes or high resource usage could be detected early and resolved quickly.”

# What happens when an application deployed on Kubernetes is not accessible, and how do you troubleshoot it?

“First, I check the pod and deployment status to ensure the application is running. I verify pod logs and events using kubectl logs and kubectl describe pod.

Next, I check the Service to confirm endpoints are created and correctly mapped to the pods.

Then I verify the Ingress controller is running and validate the Ingress configuration, including paths, host rules, and TLS settings.

After that, I check DNS routing, such as CNAME records in the domain provider, to ensure traffic is pointing to the load balancer.

Finally, I review networking components like security groups, node networking, and cluster-level networking if the issue persists.”

# A Pod is stuck in CrashLoopBackOff. How do you troubleshoot it?

First, I describe the pod to check events and understand why it is restarting.
Then I check the pod logs to identify application or startup errors.

Common causes I look for are missing or pending PVCs, port conflicts, incorrect ConfigMap or Secret values, misconfigured liveness or readiness probes, image issues like wrong tags or corrupted images, and insufficient CPU or memory.

Once the root cause is identified, I fix the configuration and redeploy the pod.

# A PVC is stuck in Pending state in EKS. How do you troubleshoot and fix it?

“First, I check the PVC events to understand why it is in Pending state.
Then I verify whether a StorageClass exists and whether it is set as default or referenced correctly in the PVC.

Next, I check if the AWS EBS CSI Driver is installed and running in the cluster.
I also verify IRSA permissions to ensure the CSI driver has access to create EBS volumes.

Finally, I confirm that the pod and node are in the same Availability Zone as the EBS volume, because EBS volumes are AZ-specific.”

# How do you troubleshoot an application that is running, but not accessible from the browser?

If it is a monolithic setup on a VM, I first check application and system logs using tools like journalctl -f or application logs to confirm the service is running and listening on the expected port.

In a microservices or Kubernetes setup, I follow a layered approach.
First, I check whether the pods are running. If not, I describe the pod and check logs to identify issues like crashes or configuration errors.

Next, I verify the Service and ensure the endpoints are correctly mapped to the pods using labels.

Then, I check whether the Ingress controller is running and validate the Ingress rules, paths, and TLS configuration.

After that, I verify DNS mapping at the domain provider level and ensure traffic is pointing to the correct LoadBalancer.
Finally, I check security groups and network rules to confirm the required ports are open.



