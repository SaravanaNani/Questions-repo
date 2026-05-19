# HR -  QUES
---

### Caarer GAP

    After my previous organization went through restructuring, 
    I used the transition period to strengthen my practical expertise in cloud and DevOps engineering 
    through hands-on work in AWS, GCP, Kubernetes, Terraform, CI/CD automation, networking, and system troubleshooting. 
    I’m now looking for a stable long-term opportunity where I can contribute with both my previous production support experience 
    and the stronger technical depth I developed during this phase.


### Self Introduction


    Hi, thank you for the opportunity.
    Myself Saravana, and I have around 2 years of experience as a DevOps Engineer.
    In my previous role at Rediffo Private Limited, I worked on both monolithic and microservices-based environments.
    On the monolithic side, I was involved in AWS infrastructure provisioning using Terraform, 
    where I created resources like EC2, VPC, and security groups.
    I also built CI/CD pipelines for Java applications using Jenkins and 
    created Docker-based Jenkins agents for consistent build environments. 
    Additionally, I assisted in troubleshooting build and deployment issues.
    On the microservices side, I worked with Kubernetes in EKS, where I helped in cluster setup, 
    configured Ingress and TLS, and supported monitoring setup using Prometheus, Grafana, and Loki. 
    I also worked on troubleshooting issues like CrashLoopBackOff and ImagePullBackOff in dev and QA environments.
    I have also gained hands-on exposure to GCP, 
    where I completed a multi-cloud project by deploying a Node.js application across AWS and GCP using Terraform and GitLab CI.
    Currently, I am looking for an opportunity where I can further enhance my skills in DevOps, 
    especially in microservices and cloud-native environments.

---
 ### multi cloud 

     worked on a multi-cloud DevOps project where the goal was to deploy applications across AWS and GCP with automation and scalability. 
     On AWS, I used ECS Fargate with ALB, and on GCP, I used Managed Instance Groups with Load Balancer. 
     The infrastructure and deployments were automated using Terraform and GitLab CI/CD. 
     In AWS, deployments are handled by updating ECS services with new Docker images, ensuring zero downtime.
     In GCP, rolling updates in MIG are used to replace instances gradually.
     For security, I implemented OIDC-based authentication in GCP to avoid storing credentials.
     Overall, the system ensures scalable, secure, and zero-downtime deployment
---
### EKS Project

    In our setup, we used AWS EKS as the Kubernetes platform, 
    where the cluster consists of a control plane managed by AWS and worker nodes running our applications.
    
    The worker nodes run multiple pods, which host our application containers. 
    We used Deployments to manage application replicas and ensure high availability.
    
    For exposing applications, we used Services like ClusterIP along with Ingress controllers to manage external traffic routing.
    
    For monitoring, we deployed Prometheus and Grafana to collect and visualize metrics, 
    and Loki with Promtail for centralized logging. Promtail runs as a DaemonSet on each node to collect logs.
    
    We also used Node Exporter and cAdvisor as DaemonSets to collect node-level and container-level metrics,
    which are scraped by Prometheus.
    
    For storage, we configured the AWS EBS CSI driver to dynamically provision volumes, 
    and we used IRSA (IAM Roles for Service Accounts) to securely grant permissions to pods.

---
### What are your strengths and weaknesses?

    ✅ Strengths (Simple & Strong):
    
    My strength is my problem-solving ability and willingness to learn. 
    I enjoy troubleshooting issues and improving systems. I also adapt quickly to new tools and technologies.
    Your weakness should be safe + improving.
    
    ✅ Better Answer:
    
    one area I am improving is communication clarity. Sometimes I try to explain things in detail, 
    but now I am working on giving more structured and clear answers.

---

### What is one key thing you carry from your previous role?”

    One key thing I carry from my previous role is hands-on experience in infrastructure automation and deployment. 
    I have worked on setting up environments end-to-end using tools like Terraform, and implemented CI/CD pipelines using Jenkins.
    I also have experience with Docker for containerization and Kubernetes for managing deployments.
    This helped me understand how to build and manage reliable and scalable infrastructure efficiently.

---
### How do you collaborate with teams?

    In one situation, Promtail was not pushing logs to Loki, which affected our logging setup. I worked with my team to troubleshoot the issue.
    
    We checked the configuration together, including labels, endpoints, and connectivity between Promtail and Loki.
    I identified that the labels and target configuration were incorrect.
    
    After fixing the configuration and redeploying, logs started flowing correctly.
    This experience showed me the importance of collaboration and communication in quickly resolving issues.

---

### Tell me a Mistake you made

    While setting up the Prometheus Operator stack, I initially didn’t realize that it required persistent storage. 
    I created a Persistent Volume Claim, but the pods were stuck in pending state because the PVC was not getting bound.
    
    Later, I understood that I needed to configure a StorageClass and install the AWS EBS CSI driver for dynamic volume provisioning.
    After setting up the EBS CSI driver and StorageClass, the PVC was successfully

---
### Tell me about a conflict you handled.?

    In one project, there was a difference in approach for setting up monitoring. 
    Initially, the team was using a manual Prometheus setup, but I suggested using the Prometheus Operator stack.
    
    I explained that the operator-based approach is better because it provides easier management, 
    automatic service discovery, and better scalability compared to manual configuration.
    
    I created a small setup in a dev environment to demonstrate the benefits. 
    After reviewing the results, the team agreed to move forward with the operator-based approach.

---
### How did you learn something new quickly?

    Initially, I had limited knowledge of Kubernetes. When I was assigned to a project, 
    I attended knowledge transfer sessions and understood the overall application architecture.
    
    I went through deployment files, YAML configurations, and learned what each section does. 
    I also explored how services, pods, and ingress were configured.
    
    To strengthen my understanding, I practiced in a test environment and applied what I learned in real tasks. 
    This helped me quickly become comfortable working with Kubernetes.

---
### Give an example of ownership.

    I took ownership of setting up the monitoring stack in a development environment.
    Instead of using a manual Prometheus setup, I implemented the Prometheus Operator along with Grafana, Loki, and Promtail.
    
    I demonstrated how the operator-based setup improves scalability, automation, and ease of management. 
    This helped the team understand the benefits and adopt a more efficient monitoring solution.
---

### How do you handle pressure?


    I haven’t faced extreme pressure situations yet, but one challenging situation I experienced was 
    when I had to quickly learn Kubernetes after being moved to a microservices project.
    
    Since it was an immediate requirement, I focused on learning quickly by attending KT sessions, 
    understanding the application setup, and practicing configurations.
    
    I stayed focused, prioritized learning, and gradually became comfortable working in the environment

---

### How have you used automation?
    I automated infrastructure provisioning using Terraform and integrated it into CI/CD pipelines. 
    This reduced manual work and ensured consistent environments across deployments.

---
### Where do you see yourself in 5 years?

    “In the next five years, I want to grow as a strong DevOps and cloud professional.
    I plan to deepen my knowledge in Kubernetes, automation, and cloud architecture.
    My goal is to become a senior DevOps engineer or cloud architect who can design scalable infrastructure and mentor other engineers.”
    
---
### Why do you want to join our company?

    “I’m interested in joining your company because the role involves working on modern cloud and container technologies. 
    In my previous role I worked on infrastructure automation, CI/CD pipelines, and Kubernetes-based deployments.
    
    From the job description, I understand that your team works with container platforms and cloud infrastructure, which matches my experience and interests.
    I believe this role will allow me to contribute my DevOps skills while also learning more about networking, security, and scalable infrastructure.”

---

### How badly do you need this job?
    
    “I am very interested in this role because it matches my experience in DevOps and cloud.
    I am looking for a stable opportunity where I can contribute and also grow technically, especially in container and cloud technologies.”

---
###  Why DevOps?
    
    “I chose DevOps because I am interested in automation and cloud technologies.
    I enjoy working with tools like Kubernetes, CI/CD, and infrastructure automation.
    DevOps allows me to solve real problems and continuously learn new technologies, which is why I am passionate about it.”

---
###  What environment do you expect?

    “I am looking for a collaborative environment where I can learn and contribute.
    I prefer a place where I can work on real infrastructure and improve my technical skills.
    Regarding leave policies, I am flexible and will follow company policies.”

---



# Devops - CEO TEC 

### 1️⃣ DevOps Trending Technologies – What do you know?

Answer:

“Currently DevOps is evolving towards automation, containerization, and cloud-native infrastructure. Some of the trending technologies include Kubernetes for container orchestration, Infrastructure as Code using tools like Terraform, CI/CD pipelines using Jenkins or GitHub Actions, monitoring tools like Prometheus and Grafana, and DevSecOps practices to integrate security into the pipeline. Multi-cloud and MLOps are also becoming popular trends in DevOps.”

### 2️⃣ Which Cloud Platform Do You Know?

    “I primarily worked on AWS cloud. I have experience with services such as EC2 for compute, 
    EKS for Kubernetes clusters, ECS for container services, and IAM for access management.
    I also worked with networking components like VPC, subnets, security groups, and load balancers.
    Recently I have started learning Google Cloud as well to expand my multi-cloud knowledge.”

### 3️⃣ Production Incident – How Do You Handle It?

    “When a production incident happens, the first step is to quickly assess the impact and identify the affected service. 
    I check monitoring dashboards and logs to understand the root cause. 
    Then I communicate with the team, apply temporary mitigation if needed, and restore service availability as quickly as possible.
    After the issue is resolved, we perform root cause analysis and implement preventive measures so the same issue does not happen again.”

### 4️⃣ Monitoring Tools – Prometheus & Grafana

    “Prometheus is used for collecting metrics from different systems using exporters like node exporter, kube-state-metrics, and cAdvisor for container metrics. 
    It scrapes metrics at regular intervals and stores them in a time-series database.
    Grafana is used to visualize these metrics through dashboards.
    This helps monitor cluster health, resource usage, and application performance.”

### 5️⃣ AWS Services – Which Ones Do You Know?


    “In AWS I have worked mainly with EC2 for compute resources, EKS for Kubernetes cluster management, ECS for container services.
    For networking I have worked with VPC, subnets, and load balancers.
    For databases I am familiar with RDS and multi-AZ deployments for high availability. 
    I also understand services like Route 53 and CloudFront for DNS and content delivery.”

### 6️⃣ On-Prem Experience - Bare Metal → VM / Cloud

“I don’t have direct hands-on experience with on-premise infrastructure yet. Most of my work has been on cloud environments like AWS. However, I understand the basic concepts such as servers, networking, and virtualization, and I’m open to learning on-prem environments as well.”

“To convert bare metal into virtual machines, we use virtualization tools like VMware, KVM, or VirtualBox.
In cloud environments, providers like AWS or Azure already give virtual machines, so we can directly launch instances like EC2.
For managing infrastructure, we can use tools like Terraform.”


### 7️⃣ Three-Tier Architecture – Why Use It?

Answer:

“A three-tier architecture separates the system into three layers: presentation layer, application layer, and database layer. This separation improves security, scalability, and maintainability. For example, web servers can be placed in public subnets while 

### 8️⃣ AWS Networking Example

If asked about architecture: Example explanation:
    
    Users access through Cloudflare or Route53
    
    Traffic goes to Load Balancer
    
    Application runs on EC2/ECS/EKS
    
    Database in RDS with Multi-AZ
    
    Cache layer with Redis
    
    Auto Scaling Group handles scaling
    data and allows independent scaling.”deployments.”
    

### 1️⃣ Difference: Ubuntu Core vs Desktop vs Server

“Ubuntu Desktop is used for normal users with GUI interface like laptops and personal use.
Ubuntu Server does not have GUI and is used for servers to run applications and services.
Ubuntu Core is a lightweight version mainly used for IoT devices and secure environments.”


### 3️⃣ Where do you use AI in DevOps / Monitoring?


“AI can be used in monitoring to detect anomalies automatically.
For example, it can identify unusual spikes in CPU or memory usage and alert before failure happens.
It can also help in log analysis to find patterns and reduce manual troubleshooting.”


### 4️⃣ How do you develop ML model (MLOps)

You answered honestly — that’s good. Just refine:

“I am still learning MLOps, but my understanding is that ML models are developed using data, trained using frameworks like TensorFlow or PyTorch, and then deployed using pipelines.
Tools like Kubeflow and CI/CD pipelines help automate training and deployment.
I am currently focusing on learning this area.”

