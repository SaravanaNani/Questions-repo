# AWS Solutions Architect – Scenario Puzzles (Case-Study Style Model Answers)

## 🟢 Compute & High Availability

1. **An application runs on EC2 and must remain available even if one AZ fails. How would you design this?**  
Deploy EC2 instances across multiple AZs behind an Application Load Balancer. Use an Auto Scaling Group spanning AZs so traffic is routed only to healthy instances.

2. **Your EC2 instance CPU spikes during traffic peaks. How do you automatically handle this?**  
Configure Auto Scaling based on CPU CloudWatch alarms so new EC2 instances are added during peak load and removed when traffic reduces.

3. **You need to run a batch job once per day. Which AWS service would you use?**  
Use an EventBridge (CloudWatch Events) rule to trigger an ECS task, Lambda, or EC2 cron job once per day.

4. **A workload needs containers but minimal infrastructure management. What compute option do you choose?**  
Use ECS with Fargate so AWS manages servers, scaling, and patching.

5. **Your application requires fast startup and short-lived execution. Which service fits best?**  
AWS Lambda, because it starts quickly and is designed for short-lived tasks.

---

## 🟢 Load Balancing & Scaling

6. **How do you distribute traffic across EC2 instances in multiple AZs?**  
Use an Application Load Balancer with targets in multiple AZs.

7. **When would you choose ALB over NLB?**  
Use ALB for HTTP/HTTPS and path-based routing. Use NLB for TCP/UDP and very low latency.

8. **How do you perform zero-downtime deployments for an EC2-based application?**  
Use rolling deployments or blue-green deployments with ALB target groups.

9. **How do you auto-scale containers running in ECS?**  
Enable ECS Service Auto Scaling based on CPU, memory, or request count metrics.

10. **What happens if a target in an ALB target group becomes unhealthy?**  
ALB stops routing traffic to it and sends traffic only to healthy targets.

---

## 🟢 Storage

11. **An application needs shared file storage across multiple EC2 instances. What service do you use?**  
Amazon EFS, as it supports shared file systems across instances.

12. **You need low-latency block storage for a database. Which AWS service fits?**  
Amazon EBS.

13. **How do you store static website files cheaply and reliably?**  
Use Amazon S3 with static website hosting and optionally CloudFront.

14. **How do you design storage for backups that must be kept for 7 years?**  
Use S3 with lifecycle rules to move data to Glacier or Glacier Deep Archive.

15. **What happens if an EC2 instance using EBS is terminated?**  
By default, the root EBS volume is deleted, but data volumes can be retained.

---

## 🟢 Databases

16. **You need a relational database with automatic failover and read scaling. What do you choose?**  
Amazon Aurora with Multi-AZ and read replicas.

17. **When would you use DynamoDB instead of RDS?**  
For massive scale, low-latency, key-value access without complex joins.

18. **How do you reduce read load on an RDS database?**  
Use read replicas or caching with ElastiCache.

19. **What happens if the primary Aurora instance fails?**  
Aurora automatically promotes a replica to primary.

20. **Your application needs millisecond latency at massive scale. Which database fits best?**  
Amazon DynamoDB.

---

## 🟢 Networking

21. **How do you design a VPC with public and private subnets?**  
Public subnets have an Internet Gateway; private subnets route traffic via NAT Gateway.

22. **Why should application servers be in private subnets?**  
For security, to prevent direct internet access.

23. **How do EC2 instances in private subnets access the internet?**  
Through a NAT Gateway.

24. **How do you securely connect on-premises infrastructure to AWS?**  
Use VPN or AWS Direct Connect.

25. **What is the difference between Security Groups and NACLs?**  
Security Groups are stateful and instance-level; NACLs are stateless and subnet-level.

---

## 🟢 Messaging & Decoupling

26. **Your application must process jobs asynchronously. Which AWS service do you use?**  
Amazon SQS.

27. **How do you ensure messages are not lost if a worker crashes?**  
Use SQS visibility timeout and retries.

28. **When would you use SNS instead of SQS?**  
When one message must notify multiple subscribers.

29. **How do ECS services communicate without tight coupling?**  
Using SQS, EventBridge, or internal service discovery.

30. **How do you design an event-driven architecture in AWS?**  
Use EventBridge or SNS to trigger Lambda or ECS tasks.

---

## 🟢 Monitoring & Reliability

31. **How do you monitor CPU, memory, and application health?**  
Use CloudWatch metrics, logs, and alarms.

32. **What actions do you take if production goes down?**  
Rollback, restore traffic, check logs/metrics, fix root cause.

33. **How do you design an application to be fault-tolerant?**  
Use Multi-AZ, auto scaling, load balancing, and retries.

34. **How do you receive alerts when an EC2 instance fails?**  
CloudWatch alarms with SNS notifications.

35. **How do you implement self-healing in AWS?**  
Use Auto Scaling Groups and health checks.

---

## 🟢 Security & IAM

36. **How do you give an EC2 instance access to S3 securely?**  
Attach an IAM role to the EC2 instance.

37. **Why should you avoid using access keys inside applications?**  
They can leak and are hard to rotate.

38. **How do you restrict traffic to an application running in AWS?**  
Use Security Groups, NACLs, and private subnets.

39. **How do you securely store database credentials?**  
Use AWS Secrets Manager or Parameter Store.

40. **What is the principle of least privilege?**  
Grant only the minimum permissions required.

---

## 🟢 CI/CD & Automation

41. **How do you automate infrastructure creation?**  
Using Terraform or CloudFormation.

42. **How do you deploy application updates without downtime?**  
Use rolling or blue-green deployments.

43. **How do you ensure the same environment across dev, test, and prod?**  
Use Infrastructure as Code and Docker.

44. **How do you roll back a failed deployment?**  
Revert to the previous version or switch traffic back.

45. **How do you automate patching of EC2 instances?**  
Use AWS Systems Manager Patch Manager.

---

## 🟢 Disaster Recovery & Backup

46. **How do you design a Multi-AZ architecture?**  
Deploy resources across multiple AZs with load balancing.

47. **What is the difference between backup and replication?**  
Backup is point-in-time copy; replication keeps data in sync.

48. **How do you recover data if a region goes down?**  
Use cross-region backups or replication.

49. **How do you design for high availability vs high durability?**  
HA ensures uptime; durability ensures data safety.

50. **What AWS services help achieve disaster recovery?**  
S3, RDS/Aurora backups, Route 53, CloudFront.

---

## 🟢 Containers & Kubernetes (ECS / EKS)

51. **When would you choose ECS over EKS?**  
When you want simpler AWS-managed container orchestration.

52. **How do you expose a containerized application to the internet?**  
Use ALB with ECS or Service/Ingress in EKS.

53. **How do you handle persistent storage for containers?**  
Use EBS, EFS, or managed databases.

54. **How do you scale container workloads automatically?**  
Use ECS Service Auto Scaling or HPA in EKS.

55. **How do you secure containers running in AWS?**  
IAM roles, security groups, image scanning, secrets management.

---

## 🟢 Cost Optimization

56. **How do you reduce EC2 costs for steady workloads?**  
Use Reserved Instances or Savings Plans.

57. **When should you use Spot Instances?**  
For fault-tolerant, non-critical workloads.

58. **How do you control unexpected AWS costs?**  
Budgets, cost alerts, and monitoring usage.

59. **How do you design a cost-optimized architecture?**  
Right-size resources, auto scaling, serverless where possible.

60. **What AWS tools help analyze and reduce costs?**  
AWS Cost Explorer, Budgets, and Trusted Advisor.

---
✅ **End of AWS SAA Case-Study Answers**
