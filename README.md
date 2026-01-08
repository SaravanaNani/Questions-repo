# Questions-repo
# Can you briefly explain your experience and what you worked on in your last role
I have worked on both monolithic and microservices-based environments.
    
In my first project, I worked mainly on AWS infrastructure automation using Terraform modules. I provisioned services like VPC, EC2, S3, and IAM, and implemented remote backend using S3 and DynamoDB. I also built CI/CD pipelines in Jenkins for Java application deployments and created Docker-based Jenkins agents to maintain a consistent build environment. I assisted in troubleshooting build failures and deployment issues using logs and Bash scripts.
    
In my second project, I supported an AWS EKS platform along with a senior engineer. My responsibilities included validating ingress and TLS configuration, setting up centralized monitoring using Prometheus, Grafana, and Loki, and ensuring workloads were running properly. I also helped troubleshoot issues like pod CrashLoopBackOff, PVC pending states, and storage-related problems.
    
Overall, my experience includes infrastructure automation, CI/CD, Kubernetes monitoring, and production issue troubleshooting. I’m now looking for a role where I can further grow and work more deeply with cloud and DevOps tools in a production environment.”

# What is Major Issues you Gone through in Production

“One major issue we faced in production was Prometheus pods going into PVC Pending state. Prometheus is stateful and requires persistent storage, but dynamic volume provisioning was not configured in the EKS cluster. After identifying the root cause, we created a StorageClass, installed the AWS EBS CSI Driver, and configured IRSA so the CSI driver could securely provision EBS volumes. Once this was done, PVCs were created automatically and Prometheus became stable.

Another issue we faced was a Jenkins server crash. We recovered the server by creating a new EC2 instance from the latest snapshot and restoring the Jenkins configuration directory. After recovery, we reconfigured access securely by recreating users, restricting network access using security groups, and ensuring only authorized IPs could access Jenkins. This helped us bring the CI/CD system back online with minimal downtime.”
