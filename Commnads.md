# DevOps Interview – Commands Cheat Sheet (Troubleshooting Focus)
This file contains most-used DevOps commands for Linux, Git, Terraform, Ansible, Docker, Kubernetes, and AWS CLI, designed for interview revision and real-time troubleshooting.
## Linux / Ubuntu Commands
### **System and resource checks**  
      
      uname -a  
      uptime  
      top  
      htop  
      ps aux  
      free -m  
      df -h  
      du -sh *

### **Process and service management**  
      
      ps -ef | grep process  
      kill -9 PID  
      systemctl status service-name  
      systemctl restart service-name  
      journalctl -u service-name  
      journalctl -xe

### **Network and port troubleshooting**  
    netstat -tulnp  
    ss -tulnp  
    curl http://localhost:8080  
    ping google.com  
    traceroute google.com
    **Files and permissions**  
    ls -l  
    chmod 755 file.sh  
    chown user:group file  
    cat file  
    tail -f logfile

## Git and GitHub Commands

### **Basic workflow**  
    
    git clone repo-url  
    git status  
    git add .  
    git commit -m "message"  
    git push origin branch-name  
    git pull origin branch-name

### **Branching**  
    
    git branch  
    git branch new-branch  
    git checkout branch-name  
    git checkout -b new-branch
    **Merge and rebase**  
    git merge branch-name  
    git rebase main

### **Undo and fix**  
    git log  
    git revert commit-id  
    git reset --soft HEAD~1  
    git reset --hard HEAD~1

# Terraform Commands

### **Basic lifecycle**  
    
    terraform init  
    terraform fmt  
    terraform validate  
    terraform plan  
    terraform apply  
    terraform destroy

### **State and backend**  
    
    terraform state list  
    terraform state show resource  
    terraform state pull  
    terraform state rm resource  
    terraform refresh

### **Lock and drift handling**  

    terraform plan  
    terraform apply  
    terraform import resource.id

**Debugging**  
      
    terraform providers lock  
    terraform console

# Ansible Commands

### **Inventory and connectivity**  
    
    ansible all -i inventory -m ping  
    ansible-inventory -i inventory --list

### **Playbook execution**  
    
    ansible-playbook playbook.yml  
    ansible-playbook playbook.yml --check  
    ansible-playbook playbook.yml --tags install  
    ansible-playbook playbook.yml --limit host

### **Debugging**        
    ansible-playbook playbook.yml -vvv

# Docker Commands

### **Images and containers**  
    
    docker images  
    docker ps  
    docker ps -a  
    docker run image  
    docker stop container  
    docker rm container

### **Logs and inspect**  
    
    docker logs container  
    docker inspect container  
    docker exec -it container bash

### **Resource issues**  
    docker stats  
    docker top container

### **Image build and cleanup**  
    
    docker build -t app .  
    docker rmi image  
    docker system prune

### **Volumes and network**  

    docker volume ls  
    docker volume inspect volume  
    docker network ls
    
# Kubernetes / EKS Commands

### **Cluster and context**  
    kubectl config get-contexts  
    kubectl config use-context cluster  
    kubectl cluster-info  
    kubectl get nodes
### **Pods**  
    kubectl get pods -A  
    kubectl describe pod pod-name  
    kubectl logs pod-name  
    kubectl logs pod-name -c container-name

### **Common pod issues**  
    kubectl exec -it pod-name -- sh  
    kubectl get events --sort-by=.metadata.creationTimestamp

### **Deployment and rollout**  
    kubectl get deploy  
    kubectl describe deploy deploy-name  
    kubectl rollout status deploy deploy-name  
    kubectl rollout undo deploy deploy-name

### **Services and networking**  
    kubectl get svc  
    kubectl describe svc svc-name  
    kubectl get endpoints

### **Ingress troubleshooting**  
    kubectl get ingress  
    kubectl describe ingress ingress-name  
    kubectl get pods -n ingress-nginx

### **Storage issues**  
    kubectl get pvc  
    kubectl describe pvc pvc-name  
    kubectl get sc
### **Resource usage**  
    kubectl top pod  
    kubectl top node

# Helm Commands

###  **Release management**  
    helm list -A  
    helm status release-name  
    helm history release-name

### **Install, upgrade, rollback**  
    helm install app chart  
    helm upgrade app chart  
    helm rollback app revision

# AWS CLI Commands

### **Identity and access**  
    aws sts get-caller-identity

### **EC2**  
    aws ec2 describe-instances  
    aws ec2 start-instances --instance-ids i-id  
    aws ec2 stop-instances --instance-ids i-id
### **S3**  
    aws s3 ls  
    aws s3 ls s3://bucket  
    aws s3 cp file s3://bucket/
### **EKS**  
    aws eks list-clusters  
    aws eks update-kubeconfig --name cluster-name --region region

# Interview Troubleshooting Order

### **Application issues**  
    Check pod  
    Check logs  
    Check service  
    Check ingress  
    Check resources  
    Rollback if needed

### **Infrastructure issues**  
    terraform plan  
    Check state  
    Fix IAM  
    Re-apply

### **CI/CD issues**  
    Check Jenkins logs  
    Check Docker build logs  
    Check artifact upload  
    Check deployment logs
    ## Key Interview Keywords
    IaC, State Locking, Idempotency, Rolling Updates, Zero Downtime, Build Once Deploy Many, Observability, IRSA, Blue-Green Deployment
