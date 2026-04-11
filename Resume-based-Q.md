
# DevOps Interview Q&A (Final Preparation)

---

## 1. Terraform Modules Structure

### Q:
In your project on infrastructure automation with Terraform, can you explain how you structured your Terraform modules for VPC, EC2, security groups, and S3? What best practices did you follow to ensure consistency and ease of use?

### Short Answer:
I used reusable Terraform modules for each resource and separate environment folders with tfvars for consistency.

### Detailed Answer:
I structured Terraform using separate modules for VPC, EC2, Security Groups, and S3. Each module contains reusable code with input variables and outputs. I created environment folders like dev, staging, and prod, where each environment calls these modules with different values using tfvars files.

For best practices, I used consistent naming, tagging, remote backend for state, and reusable modules to avoid duplication. This made the setup scalable and easy to manage.

---

## 2. Terraform + Ansible Integration

### Q:
You mentioned improving deployment speed by 40% through the integration of Terraform and Ansible in Jenkins CI/CD pipelines. Can you describe the process you used to achieve this integration and how it impacted your deployment workflow?

### Short Answer:
I integrated Terraform and Ansible in Jenkins to automate infra and deployment in one pipeline.

### Detailed Answer:
Initially, infrastructure and configuration were done separately. I integrated Terraform and Ansible into Jenkins pipelines. Terraform provisions infrastructure, and its outputs like IP addresses are passed to Ansible for server configuration and application deployment.

This reduced manual work, improved consistency, and reduced deployment time by around 40%.

---

## 3. EKS Ingress & Security

### Q:
In your role supporting AWS EKS, how did you configure ingress and load balancer services? Can you explain the considerations you took into account for securing the traffic?

### Short Answer:
I used NGINX Ingress with TLS via cert-manager and secured traffic using HTTPS and network restrictions.

### Detailed Answer:
I used NGINX Ingress Controller and Kubernetes LoadBalancer services. For security, I configured TLS using cert-manager with Let’s Encrypt. I ensured worker nodes were in private subnets and restricted traffic using security groups.

This ensured secure and controlled access to applications.

---

## 4. Observability Stack

### Q:
Can you discuss the observability stack you deployed using Prometheus, Grafana, and Loki? How did you ensure that it met the requirements for metrics collection and centralized logging?

### Short Answer:
I used Prometheus for metrics, Loki for logs, and Grafana for visualization.

### Detailed Answer:
I deployed Prometheus Operator for metrics collection along with Node Exporter, cAdvisor, and kube-state-metrics. Loki was used for log aggregation and Promtail as a DaemonSet to collect logs.

Grafana was configured with both Prometheus and Loki as data sources. I verified metrics using Prometheus targets and ensured logs were properly labeled and searchable.

---

## 5. IRSA (EBS CSI Driver)

### Q:
You've worked with IAM roles and policies in AWS. Can you explain how you configured IAM roles for service accounts per se in your EKS environment and the advantages it provided?

### Short Answer:
I used IRSA to securely provide AWS permissions to pods without credentials.

### Detailed Answer:
I created an IAM policy with required permissions for EBS operations like CreateVolume, AttachVolume, and DeleteVolume. Then I created an IAM role and configured trust with the EKS OIDC provider.

I mapped this role to a Kubernetes service account used by the EBS CSI driver. This allowed pods to access AWS securely without storing credentials.

Advantages include better security, least privilege access, and no hardcoded secrets.

---

## 6. CI/CD Troubleshooting

### Q:
In your experience with CI/CD using Jenkins, how did you handle troubleshooting Terraform validation errors and Jenkins failures? Can you provide a specific example of a problem you faced and how you resolved it?

### Short Answer:
I checked Jenkins logs and validated Terraform locally to identify and fix issues.

### Detailed Answer:
I checked Jenkins console logs to identify errors. Then I ran Terraform validate and plan locally. I found a variable mismatch and IAM permission issue. After fixing both, the pipeline worked successfully.

---

## 7. Terraform Variables Management

### Q:
Can you explain how you managed variable values when calling the modules for different environments? Specifically, how did you handle sensitive information such as IAM credentials or database passwords?

### Short Answer:
I used tfvars files for environments and stored sensitive data securely using Jenkins or AWS services.

### Detailed Answer:
I used tfvars files for each environment like dev and prod. Sensitive data like passwords and credentials were not stored in code. I used Jenkins credentials and AWS Secrets Manager.

This ensured security and proper environment separation.

---

## 8. Terraform State Management

### Q:
Can you explain how you handled state management in Terraform when integrating it with Jenkins?

### Short Answer:
I used S3 backend with DynamoDB locking.

### Detailed Answer:
I stored Terraform state in S3 and used DynamoDB for locking to prevent concurrent changes. I enabled versioning and separated state files per environment.

This ensured safe and consistent deployments.

---

## 9. NGINX Ingress + SSL

### Q:
Can you explain how you implemented the NGINX Ingress Controller for handling SSL termination with Let's Encrypt?

### Short Answer:
I used NGINX Ingress with cert-manager and Let’s Encrypt for automatic SSL.

### Detailed Answer:
I installed NGINX Ingress Controller and cert-manager. I created a ClusterIssuer using ACME protocol. Certificates were automatically generated and stored as secrets.

I faced DNS issues initially but resolved them by correcting domain mapping. Cert-manager handled automatic renewal.

---

## 10. Prometheus Service Discovery

### Q:
Can you explain how you configured service discovery in Prometheus for your observability stack?

### Short Answer:
I used ServiceMonitor and PodMonitor for dynamic discovery.

### Detailed Answer:
Prometheus Operator uses ServiceMonitor and PodMonitor to discover targets. It uses label selectors to scrape metrics from services and pods dynamically.

I verified targets using Prometheus UI and fixed issues by checking labels and endpoints.

---

## 11. IAM Policy (Least Privilege)

### Q:
Can you explain the specific steps you took to define the IAM policy for the role associated with the Kubernetes service account?

### Short Answer:
I created IAM policies with only required permissions and mapped them using IRSA.

### Detailed Answer:
I defined IAM policies with only required actions like EBS volume operations. I restricted access to specific resources and mapped the role to a Kubernetes service account.

This ensured least privilege and secure access.

---

## 12. Terraform Variable Debugging

### Q:
Can you explain the process you followed to identify the incorrect variable reference in your Terraform configuration?

### Short Answer:
I checked logs, validated Terraform, and fixed variable mismatch.

### Detailed Answer:
I checked Jenkins logs and ran Terraform validate locally. I compared variables.tf, tfvars, and usage in code. I found a naming mismatch and corrected it.

---

# 🔥 Final Tip

Start with short answer → Pause → Expand if asked
