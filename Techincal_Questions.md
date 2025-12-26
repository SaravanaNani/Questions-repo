# DevOps Interview Questions (Resume-Based)

> **Profile considered:** DevOps Engineer | ~2 years experience  
> **Core stack:** AWS, GCP, Terraform, Jenkins, Ansible, Docker, Kubernetes (EKS), CI/CD, Maven, Nexus, Prometheus/Grafana/Loki  
> **Source:** Questions filtered, merged, and deduplicated strictly from provided files, aligned to your resume :contentReference[oaicite:0]{index=0}

---

## 1. Basics

1. What is Infrastructure as Code (IaC) and why is Terraform preferred over manual provisioning?
2. What is a Terraform state file and why is it critical?
3. Difference between `terraform plan` and `terraform apply`.
4. What is a Docker image and how is it different from a container?
5. What problem does Docker solve in CI/CD pipelines?
6. What is a Jenkins pipeline and why is it used?
7. What is Ansible and what does idempotency mean?
8. What is Kubernetes and why is it used instead of Docker alone?
9. What is a Pod and why is it the smallest deployable unit in Kubernetes?
10. Difference between public and private subnets in AWS.

---

## 2. Intermediate

1. How does Terraform remote backend (S3 + DynamoDB) help teams?
2. What happens if the Terraform state file is lost or corrupted?
3. How do Terraform modules improve reusability and consistency?
4. How do you structure Terraform code for multiple environments (dev/qa/prod)?
5. Why should `terraform plan` and `apply` be separate stages in Jenkins?
6. Why should Terraform not be run directly from a developer laptop?
7. Difference between Docker COPY and ADD, and when to use each?
8. Docker volume vs bind mount — why are volumes preferred in production?
9. How does Jenkins get AWS permissions without access keys?
10. How do you ensure the same artifact is deployed across environments?
11. What is Kubernetes Deployment and how does rolling update work?
12. Difference between Deployment and StatefulSet.
13. What is a Kubernetes Service and why is it needed?
14. What is Ingress and how does it differ from a Service?
15. ConfigMap vs Secret — when do you use each?

---

## 3. Advanced

1. What is Terraform drift and how do you detect and fix it?
2. What happens if two users run `terraform apply` at the same time?
3. How does DynamoDB enable Terraform state locking?
4. What are Terraform lifecycle rules and how do they help avoid downtime?
5. Why are Terraform provisioners discouraged and what should be used instead?
6. How do you manage secrets securely in Terraform and Jenkins?
7. How do you update infrastructure using Terraform without downtime?
8. How do you reduce Docker image size for faster CI/CD pipelines?
9. How do you limit CPU and memory for containers and why is it important?
10. What is IRSA and why is it important in EKS?
11. How does Kubernetes achieve self-healing?
12. What is CrashLoopBackOff and how do you debug it?
13. How do Prometheus, Grafana, and Loki work together?
14. How do you rollback a failed Helm deployment?
15. How do you scan Docker images for vulnerabilities?

---

## 4. Scenario-Based / Real-World

1. An EC2 instance was deleted manually — what happens in Terraform and how do you fix it?
2. Terraform plan shows unexpected changes — what do you do?
3. Terraform apply failed halfway — how do you recover safely?
4. Jenkins build succeeds but the application is not accessible — how do you debug?
5. Maven build fails in Jenkins — what is your troubleshooting approach?
6. Nexus upload fails or artifact gets corrupted — how do you handle it?
7. How do you prevent two Jenkins deployments from running at the same time?
8. Deployment succeeded but the application is unstable — rollback or fix-forward?
9. Pod is running but application is not accessible externally — what do you check?
10. PVC is stuck in Pending state in EKS — how do you troubleshoot?
11. Ingress is created but traffic is not reaching the service — how do you debug?
12. Container restarts and data is lost — why did it happen and how do you fix it?
13. Docker image size is too large — how do you optimize it?
14. A node goes NotReady in EKS — what happens to the pods?
15. What improvements would you make if you redesigned the CI/CD + EKS setup from scratch?

---
