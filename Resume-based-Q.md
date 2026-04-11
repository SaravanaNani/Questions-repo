
# DevOps Interview Q&A (Final Preparation)

---

## 1. Terraform Modules Structure

### Q:In your project on infrastructure automation with Terraform, can you explain how you structured your Terraform modules for VPC, EC2, security groups, and S3? What best practices did you follow to ensure consistency and ease of use?


    I structured Terraform using separate modules for VPC, EC2, Security Groups, and S3. 
    Each module contains reusable code with input variables and outputs.
    I created environment folders like dev, staging, and prod, where each environment calls these modules with different values using tfvars files.
    For best practices, I used consistent naming, tagging, remote backend for state, and reusable modules to avoid duplication.
    This made the setup scalable and easy to manage.

---

## 2. Terraform + Ansible Integration

### Q:You mentioned improving deployment speed by 40% through the integration of Terraform and Ansible in Jenkins CI/CD pipelines. Can you describe the process you used to achieve this integration and how it impacted your deployment workflow?

    Initially, infrastructure and configuration were separate. 
    I integrated Terraform and Ansible into Jenkins pipelines. 
    Terraform provisions infrastructure, and Ansible uses dynamic inventory to automatically fetch instance details from AWS or GCP based on tags or labels.
    This removed the need to manually pass outputs like IP addresses. It made the deployment fully dynamic, scalable, and reduced manual errors.

---

## 3. EKS Ingress & Security

### Q: In your role supporting AWS EKS, how did you configure ingress and load balancer services? Can you explain the considerations you took into account for securing the traffic?

### Detailed Answer:


---
    In our EKS setup, I used a combination of ClusterIP services, NGINX Ingress Controller, and AWS LoadBalancer to expose applications in a secure way.
    
    First, the application pods are exposed internally using ClusterIP services, which means they are only accessible within the Kubernetes cluster.
    
    Then, I deployed the NGINX Ingress Controller, which acts as a central entry point for incoming traffic. 
    The Ingress resource defines routing rules, like path-based or host-based routing, to direct traffic to the correct services.
    
    The NGINX Ingress Controller is exposed externally using a Service of type LoadBalancer, which provisions an AWS NLB. 
    This allows external users to access the application.
    
    For security, I configured TLS using cert-manager with Let’s Encrypt (ACME). 
    Cert-manager automatically requests and renews SSL certificates, and the certificates are stored as Kubernetes secrets.
    NGINX uses these secrets to handle HTTPS traffic.
    
    Additionally, I ensured that: 
    
    Worker nodes are in private subnets, so they are not directly exposed to the internet
    Only the LoadBalancer is public
    Security groups restrict inbound traffic to required ports (like 80 and 443)
    
    This setup ensures that all traffic flows securely:
    User → LoadBalancer → NGINX Ingress → ClusterIP Service → Pods
    Overall, this architecture provides secure, scalable, and controlled access to applications.



## 4. Observability Stack

### Q:Can you discuss the observability stack you deployed using Prometheus, Grafana, and Loki? 
###How did you ensure that it met the requirements for metrics collection and centralized logging?

    In our Kubernetes environment, I implemented a complete observability stack using Prometheus, Grafana, Loki, and Promtail to handle both metrics and logs.
    
    For metrics collection, I deployed the Prometheus Operator (kube-prometheus-stack), which simplifies setup and management.
    
    I used:
    
    Node Exporter to collect node-level metrics like CPU, memory, and disk
    cAdvisor to collect container-level metrics
    kube-state-metrics to collect Kubernetes object metrics like pod status, deployments, and replicas
    
    These components were deployed as DaemonSets or services, and Prometheus used ServiceMonitor and PodMonitor to dynamically discover 
    and scrape metrics from them.
    
    For logging, I used Loki as the log aggregation system and Promtail as a DaemonSet running on each worker node.
    Promtail collects logs from containers and system paths, attaches labels like pod name, namespace, and container, and sends them to Loki.
    
    In Grafana, I configured both Prometheus and Loki as data sources, which allowed us to:
    
    Visualize metrics using dashboards (CPU, memory, pod health)
    View logs and correlate them with metrics for troubleshooting
    
    To ensure everything was working correctly:
    
    I verified targets in the Prometheus /targets page to confirm all exporters were being scraped
    Checked logs in Loki using labels to ensure proper indexing
    Created dashboards and alerts for key metrics like CPU usage and pod failures
    This setup provided real-time monitoring, centralized logging, and faster issue resolution, making the system more observable and reliable.
---

## 5. IRSA (EBS CSI Driver)

### Q: You've worked with IAM roles and policies in AWS. Can you explain how you configured IAM roles for service accounts per pod in your EKS environment and the advantages it provided?

    I used IRSA to securely provide AWS permissions to pods without credentials.
    I created an IAM policy with required permissions for EBS operations like CreateVolume, AttachVolume, and DeleteVolume.
    Then I created an IAM role and configured trust with the EKS OIDC provider.
    
    I mapped this role to a Kubernetes service account used by the EBS CSI driver. 
    This allowed pods to access AWS securely without storing credentials.
    
    Advantages include better security, least privilege access, and no hardcoded secrets.

---

## 6. CI/CD Troubleshooting

### Q:In your experience with CI/CD using Jenkins, how did you handle troubleshooting Terraform validation errors and Jenkins failures?
### Can you provide a specific example of a problem you faced and how you resolved it?


    I checked Jenkins console logs to identify errors. 
    Then I ran Terraform validate and plan locally. 
    I found a variable mismatch and IAM permission issue. 
    After fixing both, the pipeline worked successfully.

---

## 7. Terraform Variables Management

### Q: Can you explain how you managed variable values when calling the modules for different environments? 
### Specifically, how did you handle sensitive information such as IAM credentials or database passwords?

    I used tfvars files for each environment like dev and prod. Sensitive data like passwords and credentials were not stored in code. 
    I used Jenkins credentials and AWS Secrets Manager.
    This ensured security and proper environment separation.

---

## 8. Terraform State Management

### Q: Can you explain how you handled state management in Terraform when integrating it with Jenkins?


    I stored Terraform state in S3 and used DynamoDB for locking to prevent concurrent changes.
    I enabled versioning and separated state files per environment.
    This ensured safe and consistent deployments.

---

## 9. NGINX Ingress + SSL

### Q: Can you explain how you implemented the NGINX Ingress Controller for handling SSL termination with Let's Encrypt?

    In our setup, I used NGINX Ingress Controller with cert-manager and Let’s Encrypt to handle SSL termination automatically.
    
    First, I installed cert-manager in the cluster and created a ClusterIssuer,
    which defines how certificates should be requested from Let’s Encrypt using the ACME protocol.
    
    The ClusterIssuer includes:
    
    Let’s Encrypt server URL
    Email for registration
    Private key reference
    HTTP-01 challenge configuration using NGINX Ingress
    
    Once the ClusterIssuer is created, I updated the Ingress resource with annotations to reference this ClusterIssuer and enabled TLS by specifying a secret name.
    
    When this Ingress is applied:
    
    cert-manager detects the request
    It contacts Let’s Encrypt and initiates the ACME challenge (HTTP-01)
    NGINX Ingress temporarily serves the challenge response
    Let’s Encrypt validates the domain
    cert-manager generates the certificate and stores it as a Kubernetes TLS secret
    
    NGINX Ingress then uses this secret to terminate HTTPS traffic.
    
    For renewal, cert-manager automatically renews certificates before expiry without downtime.
    
    Initially, I faced issues with DNS not pointing correctly to the LoadBalancer, so the ACME challenge failed. After fixing DNS mapping, 
    the certificate was issued successfully.
    
    This setup ensures fully automated SSL management, secure traffic, and zero manual intervention.


🔥 Short Version

I created a ClusterIssuer with ACME config, referenced it in Ingress, cert-manager handled challenge validation, 
generated TLS secret,and NGINX used it for HTTPS.


---

## 10. Prometheus Service Discovery

### Q: Can you explain how you configured service discovery in Prometheus for your observability stack?


    Prometheus Operator uses ServiceMonitor and PodMonitor to discover targets. 
    It uses label selectors to scrape metrics from services and pods dynamically.
    I verified targets using Prometheus UI and fixed issues by checking labels and endpoints.
    
---

## 11. IAM Policy (Least Privilege)

### Q:Can you explain the specific steps you took to define the IAM policy for the role associated with the Kubernetes service account?


    I created IAM policies with only required permissions and mapped them using IRSA.
    I defined IAM policies with only required actions like EBS volume operations. 
    I restricted access to specific resources and mapped the role to a Kubernetes service account.
    This ensured least privilege and secure access.

---

## 12. Terraform Variable Debugging

### Q: Can you explain the process you followed to identify the incorrect variable reference in your Terraform configuration?

    I first checked the Jenkins console logs to identify the exact error and where the failure occurred. 
    Then I ran terraform validate and terraform plan locally to reproduce the issue and get a clearer understanding. 
    I verified the variable definitions in variables.tf, compared them with how they were used in the main configuration and tfvars files, 
    and found a naming mismatch. After correcting the variable reference, the validation passed and the pipeline executed successfully.---

## 13. Dockerfile (Java WAR)

```dockerfile

FROM maven:3.9.6-eclipse-temurin-17 AS build
WORKDIR /app
COPY . .
RUN mvn clean package

FROM tomcat:9-jdk17
COPY --from=build /app/target/*.war /usr/local/tomcat/webapps/app.war

```
### 14. DOcker compose file with 2 serivces with diffrent image
    
    version: '3'
    services:
      app:
        image: myapp:latest
        ports:
          - "8080:8080"
    
      db:
        image: mysql:5.7
        environment:
          MYSQL_ROOT_PASSWORD: root

### 15. create user without useradd andadduser

    echo "test:x:1001:1001::/home/test:/bin/bash" >> /etc/passwd

###  16. copy with time stamp and chown onwership to a driectory recusively 

    cp -p file1 file2  
    chown -R user:group dir/

###     
