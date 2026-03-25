### application is deployed in Kubernetes. Pods are Running, but users cannot access the application.
    
    I will debug this step by step starting from logs.
    
    First, I will check application logs using kubectl logs to identify any application or dependency errors.
    
    Then, I will check pod details using kubectl describe pod to verify events, restarts, and probe failures.
    
    Next, I will verify the service by checking selectors and endpoints to ensure traffic is routed correctly.
    
    Then, I will test internal connectivity by exec into the pod and using curl to confirm if the application is responding.
    
    If internal connectivity works, I will move to check ingress and external access configuration.
    
    After that, I will verify network configurations like security groups or firewall rules.
    
    Finally, I will check dependencies like database connectivity and node-level issues if required.


---
### CarshLoopBackOff


    CrashLoopBackOff means the container is repeatedly crashing and Kubernetes is trying to restart it.
    
    Common reasons:
    1. Application error or misconfiguration
    2. Liveness probe failure
    3. Resource limits (OOMKilled)
    
    Debugging steps:
    
    First, I will check application logs using kubectl logs to identify the exact error.
    
    Then, I will describe the pod using kubectl describe pod to check events and see if it is OOMKilled or probe failures.
    
    Next, I will verify configuration such as environment variables, configmaps, and secrets.
    
    Then, I will check liveness and startup probe configuration to ensure it is not killing the container early.
    
    Finally, I will check resource limits and usage to see if the container is running out of memory.

---
### Application is running but suddenly becomes very slow

    I will debug this step by step.
    
    First, I will check application logs to identify slow queries, timeout errors, or dependency failures.
    
    Then, I will check dependencies like database performance, query latency, and external API response time.
    
    Next, I will check pod or process-level resource usage using kubectl top pod or top command to identify high CPU or memory usage.
    
    Then, I will check load average using uptime to see if the system is overloaded.
    
    After that, I will check disk I/O using iostat to identify any I/O bottlenecks.
    
    Next, I will verify network latency between services using curl or ping.
    
    Finally, I will check if there is increased traffic and whether autoscaling is working correctly.

--- 
### Pod Pending state?


Pod Pending means the pod is not yet scheduled on any node.
    Common reasons:
    - Insufficient CPU or memory resources
    - Node selector or affinity mismatch
    - Taints and tolerations issue
    - PVC not bound
    - Node not ready
    
    Debug steps:
    
    First, I will describe the pod using kubectl describe pod to check events, especially for FailedScheduling messages.
    
    Then, I will verify node status using kubectl get nodes to ensure nodes are in Ready state.
    
    Next, I will check resource availability in the cluster to see if there is enough CPU and memory.
    
    Then, I will verify node selectors, affinity rules, and taints/tolerations.
    
    Finally, I will check if any persistent volume claim is in pending state.
    

### Your application is working internally, - but it is not accessible externally

    I will debug this step by step.
    
    Since the application is working internally, I confirm that the pod and application are healthy.
    
    Next, I will focus on external exposure.
    
    First, I will check the service configuration:
    - Verify the service type (NodePort or LoadBalancer)
    - Ensure targetPort and port are correctly mapped
    
    Then, I will check if the service has a valid external IP or LoadBalancer is provisioned.
    
    Next, I will verify ingress configuration and ingress controller logs if ingress is used.
    
    After that, I will test external access using curl from outside the cluster.
    
    Finally, I will check network configurations like security groups, firewall rules, and open ports to ensure traffic is allowed.


### Pods running, but inter-service communication failing

    
    I will debug this step by step.
    
    First, I will check application logs to identify connection errors or DNS resolution failures.
    
    Then, I will verify DNS resolution using nslookup from inside the pod to ensure the service name resolves to a ClusterIP.
    
    Next, I will check the service configuration and ensure selectors match pod labels.
    
    Then, I will verify endpoints using kubectl get endpoints to ensure pods are correctly registered.
    
    After that, I will test connectivity using curl from one pod to another service.
    
    Next, I will check if any network policies are blocking communication.
    
    Finally, I will verify that the application is listening on the correct port.

### Docker Container is Running -APP is not responding

 
    I will debug this step by step.
    
    First, I will check container logs using docker logs to identify any application errors.
    
    Then, I will verify if the application process is running inside the container by exec into it.
    
    Next, I will check if the application is listening on the correct port using netstat.
    
    Then, I will verify port mapping between container and host to ensure the port is exposed correctly.
    
    After that, I will test connectivity using curl from inside the container and from the host.

### EC2 instance CPU is 100% and application is down

    I will debug this step by step.
    
    First, I will check CloudWatch metrics or SSH into the EC2 instance and use top to identify high CPU consuming processes.
    
    Then, I will check load average using uptime to confirm if the system is overloaded.
    
    Next, I will identify the process causing high CPU usage using ps or top.
    
    Then, I will analyze whether the spike is expected (high traffic) or due to an issue like a bug or infinite loop.
    
    If it is an issue, I will optimize or restart the process.
    
    After that, I will check application logs to ensure the application is functioning correctly.
    
    Finally, if the load is due to increased traffic, I will scale the system using Auto Scaling Groups or increase instance capacity.
    
    Next, I will check if the application is bound to 0.0.0.0 and not just localhost.
    
    Finally, I will verify network configuration such as bridge or host mode and ensure no firewall is blocking access.

### Jenkins pipeline failed while pushing Docker image to ECR


    I will debug this step by step.
    
    First, I will check Jenkins logs to identify the exact error message during the push.
    
    Then, I will verify AWS authentication:
    - Ensure Jenkins has correct IAM role or credentials
    - Check permissions for ECR (ecr:PutImage, ecr:GetAuthorizationToken)
    
    Next, I will check if docker login to ECR is successful.
    
    Then, I will verify that the ECR repository exists and the image is tagged correctly.
    
    After that, I will check network connectivity from Jenkins to ECR.
    
    Finally, I will ensure there are no issues with Docker daemon or disk space on the Jenkins node.

### RDS database is slow in production
    
    I will debug this step by step.
    
    First, I will check application logs to identify slow queries or timeout errors.
    
    Then, I will analyze database performance using CloudWatch and Performance Insights, checking CPU, memory, IOPS, and connections.
    
    Next, I will check for long-running queries, locks, or missing indexes.
    
    Then, I will verify the number of active connections and connection saturation.
    
    After that, I will check for I/O bottlenecks or storage limitations.
    
    Finally, I will optimize queries, add indexes, use caching like Redis, or scale the RDS instance if required.

### 1. No logs, app down - “Application is down but logs show nothing — what will you do?”

### 2. Intermittent issue - “App works sometimes, fails sometimes”

### 3. CPU normal, app slow - “No infra issue, still slow”

### 4. After deployment issue - “App broke after new release”

### 5. Node failure - “Kubernetes node goes down — what happens?”    
