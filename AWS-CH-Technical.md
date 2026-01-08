# End-to-End Cloud & DevOps Interview Preparation (Resume + AWS SAA + JD Based)

## 1. Resume-Based Scenario Questions & Answers

**Q1. You automated AWS infrastructure using Terraform. What issues did you face?**  
I faced Terraform validation errors, state conflicts, and IAM permission issues. I resolved them by fixing variable values, using S3 and DynamoDB as a remote backend for state locking, and updating IAM permissions.

**Q2. Why did you use S3 + DynamoDB as a remote backend?**  
S3 stores the Terraform state centrally, and DynamoDB provides state locking to prevent concurrent updates.

**Q3. Jenkins pipeline failed during deployment. How did you debug it?**  
I checked Jenkins console logs, Maven build output, Docker image creation, and added Bash scripts to verify application and server health after deployment.

**Q4. Why did you use Docker-based Jenkins agents?**  
To maintain a consistent build environment and avoid dependency and version mismatch issues.

**Q5. Prometheus pod went into CrashLoopBackOff. What happened?**  
Prometheus is stateful and requires persistent storage. The PVC was pending because storage was not provisioning automatically.

**Q6. How did you fix the Prometheus storage issue?**  
I configured a StorageClass, installed the AWS EBS CSI Driver, and set up IRSA so EKS could provision storage dynamically.

**Q7. Why did you later move to Prometheus Operator?**  
The operator simplifies management and enables dynamic scraping using ServiceMonitor and PodMonitor.

**Q8. PVC stuck in Pending state. What did you check?**  
PVC events, StorageClass configuration, EBS CSI driver pods, IAM permissions, and node AZ alignment.

**Q9. Ingress not working. How did you debug it?**  
I checked pod health, service endpoints, ingress rules, ingress controller status, LoadBalancer/DNS, and TLS certificate readiness.

**Q10. What was your role in EKS platform support?**  
I validated ingress, TLS, monitoring, storage, and assisted in troubleshooting pod, PVC, and Helm issues with senior engineers.

---

## 2. JD-Based Questions (Precision Group)

**Q11. How do you deploy an EC2 instance?**  
Select AMI, instance type, VPC and subnet, security group, key pair, and launch the instance.

**Q12. What do you check if EC2 is not reachable?**  
Instance state, security group rules, route table, internet gateway, and NACLs.

**Q13. What do you monitor using CloudWatch?**  
CPU, memory, disk usage, logs, alarms, and service health.

**Q14. Why do you use Terraform?**  
To automate infrastructure provisioning, reduce manual errors, and manage infrastructure as code.

**Q15. Explain your Jenkins CI/CD flow.**  
Code checkout, build and test, Docker image build, push to registry, deploy, and validate.

**Q16. What automation scripts have you written?**  
Bash scripts for health checks and disk usage, and Python scripts for API and service validation.

**Q17. How do you secure AWS infrastructure?**  
Using IAM least privilege, security groups, no hardcoded secrets, Secrets Manager, and monitoring.

**Q18. How do you handle vulnerabilities and patching?**  
By scanning images, applying OS patches, and updating container images.

**Q19. How do you collaborate with L2/L3 teams?**  
I gather logs and metrics, reproduce issues, and share clear findings to help resolve problems faster.

---

## 3. AWS Solutions Architect – Associate Case Studies

**Q20. Design a highly available web application on AWS.**  
Use Route 53, ALB, Auto Scaling EC2/ECS across multiple AZs, RDS Multi-AZ, and CloudWatch.

**Q21. What happens if one AZ goes down?**  
ALB routes traffic to healthy AZs, Auto Scaling replaces instances, and RDS Multi-AZ fails over automatically.

**Q22. How do you handle sudden traffic spikes?**  
Use Auto Scaling, ALB, and optionally SQS to buffer requests.

**Q23. Production is down. What actions do you take?**  
Check monitoring alerts and logs, verify ALB target health, rollback if needed, scale resources, and inform stakeholders.

---

## 4. Tricky AWS SAA Scenario Questions

**Q24. EC2 needs secure access to S3. How do you do it?**  
Attach an IAM role with S3 permissions to the EC2 instance.

**Q25. You need shared storage for multiple EC2 instances. What do you use?**  
Amazon EFS.

**Q26. How do you deploy without downtime?**  
Use Blue-Green or Rolling deployments with a load balancer.

**Q27. When do you use SQS?**  
For asynchronous processing and decoupling services.

**Q28. When do you choose DynamoDB?**  
For low-latency, high-throughput key-value workloads.

**Q29. How do you host a static website globally?**  
Use S3 with CloudFront.

**Q30. How do you store secrets securely?**  
Use AWS Secrets Manager or Parameter Store.

---

## 5. Mock Architect-Level Communication Examples

**Q31. Why do you prefer managed services?**  
Managed services reduce operational overhead and improve reliability.

**Q32. Explain how AWS services interlink in your architecture.**  
User → Route 53 → ALB → EC2/ECS → RDS/S3 → CloudWatch.

---

## Key Terms to Use in Interviews
Highly available, fault tolerant, multi-AZ, scalable, least privilege, automated, observability
