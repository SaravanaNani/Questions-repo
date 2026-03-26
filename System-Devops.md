# Universal Debug Template :
    
    1. Is app running? (Process / Pod)
    
    2. Is it reachable internally? (curl)
    
    3. Is it exposed? (Service / Port / LB)
    
    4. Are dependencies working? (DB / API)
    
    5. Any infra issue? (CPU / Memory / Network)



### Monlythic Debug 
---

## WIth out ALB

User → EC2 → App → DB
    
    - App running?
    - Port open?
    - Security group?

Logs -> Process (systemctl, ps -a | grep service name) -> Port Open SG & Binding -> DB (Dependency ) Autheniction -> Infra (laod avg , cpu, bottle neck)


## With ALB

User → ALB → Target Group → EC2 (ASG) → App

1. ALB working? is ALB Reachable - curl http://<ALB-DNS>

   ❌ If NOT reachable:
        
        DNS issue
        ALB listener not configured
        Security group blocking

2. Target group healthy? AWS console -> Healthy / Unhealthy targets

3. Health check path correct?

Case 1: ALL targets unhealthy

    👉 Root causes:
    
    - Health check path wrong (/health)
    - App not running
    - Port mismatch
    
    Example
        
    Health check = /health
    App exposes /api
    
    👉 Result:
    
    Unhealthy ❌

Case 2 - Targets healthy but app not working  

    Check Health Check Config
    
    👉 Verify:
    
    Path: /health
    Port: 80 / 8080
    Protocol: HTTP
    
    🔥 Important
    ALB calls → EC2:PORT/health
    ❌ If wrong:
    
    👉 Fix path/port

4. EC2 app running?

Even with ASG, you STILL check EC2 (important)
    
    curl localhost:PORT
    netstat -tulnp
    systemctl status app

    ❌ Possible issues:
    - App not running
    - Port wrong
    - App bound to 127.0.0.1

6. Security group?

        👉 Check: ALB → EC2 (port open?)
                  User → ALB (port open?)
       ❌ Example:
            ALB allows 80
            EC2 blocks 80
            
       👉 Result: Traffic blocked ❌


---
### ECS Fargate + ALB (Full Flow)

User → ALB → Target Group → Fargate Task (Container) → App

    ✅ 1. Fargate Task starts
    
    👉 AWS automatically:
    
    Creates ENI (network interface)
    Assigns private IP
    
    Task → gets private IP automatically
    
    ✅ 2. Target Group registers task
    
    Target type = Farigate IP  ** 👉 Not instance Type (important!) **
    
    ✅ 3. ALB sends traffic
    
    ALB → Target Group → Task IP:PORT
    
    🧪 Health Check Flow
    
    ALB → /health → Task IP:8080

    🔥 Important - Same as EC2:
    
    Path = /health
    Port = container port (e.g., 8080)

🧠 Security Groups in Fargate

        🔹 ALB SG
        Inbound: 0.0.0.0/0 → 80/443
        🔹 Fargate Task SG
        Inbound: Source = ALB SG
        Port = 8080
        
        🎯 Same concept as EC2!
        ALB → allowed
        Others → blocked
        
❗ Important Differences
    
    🔥 1. No EC2
    So: No SSH ❌No netstat ❌

    🔥 2. Debugging changes
    
    👉 You check:
    
    - Container logs (CloudWatch)
    - Task status
    - Health check
    - SG

    
🧠 Debug Flow (Fargate)
        
        1. ALB reachable?
        2. Target group healthy?
        3. Health check path correct?
        4. Task running?
        5. Logs (CloudWatch)
        6. SG correct?
        
🧪 Real Issue Example

    ❌ Problem
    
    Targets unhealthy
    
    Root cause: Health check path wrong (/health vs /api)
    
    Fix: Update target group health check path



---

### Microservices Debug [Trace] 
---

User → Ingress → Service → Pod → DB

Step 1: App running?

    EC2 → systemctl status
    K8s → kubectl get pods

Step 2: Internal works?

    curl localhost:8080
    ❌ Not working → app issue
    ✅ Working → go next

Step 3: External works?
    
    EC2 → port open?
    K8s → service/ingress

Step 4: Dependency?

    DB slow?
    API down?

Step 5: Infra?
    
    CPU
    Memory
    Network

pod Logs -> pod Events -> Services/ Endpoints -> Ingress - N/W SG -> Depedency -> Infra (Nodes CPU etc)

---

Metrics → WHAT is wrong - USed for Monitoring and Alrets

    Memory: 70%    Latency: 200ms
    Requests/sec: 500
  
Logs → WHY it is wrong  - Used for exact errors and Debugging 
    
    ERROR: DB connection failed
    Timeout while calling API

    Appplication Error - Port used | config issues
    Dependency Error - DB connection refuesed | API TIme Out | Authenticatio failed
    Resource Error - OOM Error | Disk Full | To Many connections
    

Traces → WHERE it is wrong - Used for Where delay happened - Microservices debugging
     
    User request → API → Service A → DB → Response

---
What is Latency?

    You click login
    Response comes in 100ms → fast ✅
    Response comes in 2 seconds → slow ❌
    
    👉 That delay = latency
---

2. What is Rate Limiting?


        Limit how many requests a user can send
        Rate limiting protects the system by restricting excessive requests.
   
        🧪 Example
        Max 100 requests per minute per user
        
        👉 Prevents: Overload and Attacks

---
3.  What is memeory Leak?

        Application keeps using memory but never releases it
        
        🧪 Example
        App starts → uses 200MB
        After 1 hour → 500MB
        After 2 hours → 1GB ❌
        
        👉 Memory keeps increasing = memory leak
        
        🔥 Why it happens
        - Objects not cleared
        - Unclosed DB connections
        - Cache not cleaned
        - Infinite data growth
        ⚠️ What happens
        Memory full → system slows → crash → restart

---   

### 1. Application is deployed in Kubernetes. Pods are Running, but users cannot access the application.
    
    I will debug this step by step starting from logs.
    
    First, I will check application logs using kubectl logs to identify any application or dependency errors.
    
    Then, I will check pod details using kubectl describe pod to verify events, restarts, and probe failures.
    
    Next, I will verify the service by checking selectors and endpoints to ensure traffic is routed correctly.
    
    Then, I will test internal connectivity by exec into the pod and using curl to confirm if the application is responding.
    
    If internal connectivity works, I will move to check ingress and external access configuration.
    
    After that, I will verify network configurations like security groups or firewall rules.
    
    Finally, I will check dependencies like database connectivity and node-level issues if required.


---
### 2. CarshLoopBackOff


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
### 3. Application is running but suddenly becomes very slow

    I will debug this step by step.
    
    First, I will check application logs to identify slow queries, timeout errors, or dependency failures.
    
    Then, I will check dependencies like database performance, query latency, and external API response time.
    
    Next, I will check pod or process-level resource usage using kubectl top pod or top command to identify high CPU or memory usage.
    
    Then, I will check load average using uptime to see if the system is overloaded.
    
    After that, I will check disk I/O using iostat to identify any I/O bottlenecks.
    
    Next, I will verify network latency between services using curl or ping.
    
    Finally, I will check if there is increased traffic and whether autoscaling is working correctly.

--- 
### 4. Pod Pending state?

    
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
    
---
### 5. Your application is working internally, - but it is not accessible externally

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

---
### 6. Pods running, but inter-service communication failing

    
    I will debug this step by step.
    
    First, I will check application logs to identify connection errors or DNS resolution failures.
    
    Then, I will verify DNS resolution using nslookup from inside the pod to ensure the service name resolves to a ClusterIP.
    
    Next, I will check the service configuration and ensure selectors match pod labels.
    
    Then, I will verify endpoints using kubectl get endpoints to ensure pods are correctly registered.
    
    After that, I will test connectivity using curl from one pod to another service.
    
    Next, I will check if any network policies are blocking communication.
    
    Finally, I will verify that the application is listening on the correct port.
---
### 7. Docker Container is Running -APP is not responding

 
    I will debug this step by step.
    
    First, I will check container logs using docker logs to identify any application errors.
    
    Then, I will verify if the application process is running inside the container by exec into it.
    
    Next, I will check if the application is listening on the correct port using netstat.
    
    Then, I will verify port mapping between container and host to ensure the port is exposed correctly.
    
    After that, I will test connectivity using curl from inside the container and from the host.
---
### 8. EC2 instance CPU is 100% and application is down

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
---
### 9. Jenkins pipeline failed while pushing Docker image to ECR


    I will debug this step by step.
    
    First, I will check Jenkins logs to identify the exact error message during the push.
    
    Then, I will verify AWS authentication:
    - Ensure Jenkins has correct IAM role or credentials
    - Check permissions for ECR (ecr:PutImage, ecr:GetAuthorizationToken)
    
    Next, I will check if docker login to ECR is successful.
    
    Then, I will verify that the ECR repository exists and the image is tagged correctly.
    
    After that, I will check network connectivity from Jenkins to ECR.
    
    Finally, I will ensure there are no issues with Docker daemon or disk space on the Jenkins node.
---
### 10. RDS database is slow in production
    
    I will debug this step by step.
    
    First, I will check application logs to identify slow queries or timeout errors.
    
    Then, I will analyze database performance using CloudWatch and Performance Insights, checking CPU, memory, IOPS, and connections.
    
    Next, I will check for long-running queries, locks, or missing indexes.
    
    Then, I will verify the number of active connections and connection saturation.
    
    After that, I will check for I/O bottlenecks or storage limitations.
    
    Finally, I will optimize queries, add indexes, use caching like Redis, or scale the RDS instance if required.
---
### 11. No logs, app down - “Application is down but logs show nothing — what will you do?”
    
    I will debug this step by step.
    
    Since there are no logs, I suspect the application is not starting or failing before logging.
    
    First, I will check if the application process is running using ps or systemctl.
    
    If the process is not running, I will check startup issues like configuration errors, environment variables, or missing dependencies.
    
    If the process is running, I will check if the application is listening on the expected port using netstat.
    
    Then, I will test local connectivity using curl to verify if the application is responding.
    
    If it is not responding, I will check for port conflicts or binding issues.
    
    Finally, if the application is running but still not accessible, I will move to service, network, and infrastructure checks.
---
### 12. Intermittent issue - “App works sometimes, fails sometimes”
    
    I will debug this step by step.
    
    First, I will check application logs to identify patterns of failure, such as timeouts or errors.
    
    Then, I will check if the issue correlates with specific times or traffic spikes.
    
    Next, I will analyze resource usage like CPU, memory, and load to identify spikes or leaks.
    
    Then, I will check dependencies such as database or external APIs for latency or connection limits.
    
    After that, I will verify network stability and latency between services.
    
    Finally, I will check if autoscaling is configured properly to handle traffic fluctuations.
---
### 13. CPU normal, app slow - “No infra issue, still slow”


    I will debug this step by step.
    
    Since CPU is normal, I will focus on non-CPU bottlenecks.
    
    First, I will check application logs to identify slow responses, timeouts, or dependency-related errors.
    
    Then, I will analyze dependencies such as database performance, including slow queries, locks, or connection limits.
    
    Next, I will check disk I/O using iostat to identify any I/O bottlenecks.
    
    After that, I will verify network latency to ensure there are no delays in communication between services.
    
    Finally, I will check memory usage and confirm the application is not waiting on external resources or experiencing thread blocking.

---
    
### 14. After deployment issue - “App broke after new release”

    I will debug this step by step.
    
    Since the issue occurred after a new deployment, I will first suspect changes in the new release.
    
    First, I will check application logs to identify errors introduced in the new version.
    
    Then, I will compare the new deployment with the previous working version to identify changes in code, configuration, or environment variables.
    
    If the issue is critical, I will immediately roll back to the previous stable version to restore service.
    
    Next, I will test the new version in a staging or test environment to reproduce the issue.
    
    Then, I will check for configuration issues, dependency mismatches, or resource limits introduced in the new deployment.
    
    Finally, I will fix the root cause and redeploy safely.

---
### 15. Node failure - “Kubernetes node goes down — what happens?”    

        When a Kubernetes node goes down, the node becomes NotReady.
        
        After a grace period, Kubernetes marks the pods on that node as failed.
        
        For stateless applications managed by Deployment or ReplicaSet, pods are automatically rescheduled on other healthy nodes.
        
        For stateful applications using StatefulSet, pods are also recreated on another node, and volumes like EBS are detached and reattached, which may cause some downtime.
        
        If pods are using node-specific storage like hostPath, they cannot be rescheduled until the node recovers.
        
        Overall, Kubernetes ensures availability by rescheduling pods on healthy nodes, but recovery time depends on workload type and storage configuration.
---

### 16. App is slow, CPU and Logs are Normal, whats next ?
    
    I will debug this step by step.
    
    Since CPU and logs are normal, I suspect the application is waiting on external resources.
    
    First, I will check database performance, including slow queries, locks, and connection limits.
    
    Then, I will check network latency between services or external APIs.
    
    Next, I will check disk I/O to identify any I/O bottlenecks.
    
    After that, I will check memory usage and ensure there is no swapping or memory pressure.
    
    Finally, I will analyze thread dumps to see if the application threads are blocked or waiting on resources.

---  

### 17. How will you debug without access to logs? 

    I will debug this step by step.
    
    Since logs are not available, I will rely on metrics and system behavior.
    
    First, I will check application health using load balancer health checks.
    
    Then, I will analyze metrics from CloudWatch or Grafana such as CPU, memory, latency, and error rates.
    
    Next, I will check resource usage on the server or pod to identify any bottlenecks.
    
    Then, I will test connectivity using curl or API endpoints to verify response behavior.
    
    After that, I will check dependencies like database or external APIs using monitoring metrics.
    
    Finally, I will check infrastructure signals like node health, scaling events, and network issues.

### 18. How do you design zero downtime deployment?

        I will ensure zero downtime using controlled deployment strategies.
        
        In AWS/monolithic setup:
        - Deploy application across multiple AZs behind a Load Balancer
        - Use health checks to route traffic only to healthy instances
        - Use blue-green or canary deployment to shift traffic gradually
        
        In Kubernetes:
        - Use rolling updates with maxUnavailable and maxSurge
        - Configure readiness probes so traffic goes only to ready pods
        - Ensure multiple replicas for high availability
        
        This ensures no downtime during deployment.

### 19. How do you handle traffic spike?

        
        I will handle traffic spikes using scaling and traffic distribution.
        
        First, I will monitor metrics like request rate and latency.
        
        Then, I will use Auto Scaling Groups or Kubernetes HPA to scale the application horizontally.
        
        Next, I will distribute traffic using a Load Balancer across multiple instances or pods.
        
        After that, I will use caching (like Redis or CDN) to reduce load on the application and database.
        
        Finally, I will implement rate limiting to protect the system from overload.

### 20. How do you debug a memory leak?
    
    I will debug this step by step.
    
    First, I will monitor memory usage over time to identify if it is continuously increasing.
    
    Then, I will check application logs for memory-related errors.
    
    Next, I will analyze whether the application is not releasing resources like connections or objects.
    
    Then, I will check for restart patterns, such as frequent OOMKilled events.
    
    Finally, I will optimize the application or restart the service temporarily to recover.
