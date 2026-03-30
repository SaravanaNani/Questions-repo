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
# DevOps Interview Answers (1–35) — Speaking Format

---

## 1. Kubernetes app running but not accessible

If the app is running but not accessible, I check service exposure.

* Verify Service type (ClusterIP/NodePort/LoadBalancer)
* Check labels & selectors
* Check endpoints
* Verify Ingress rules
* Check pod logs and ports

---

## 2. CrashLoopBackOff

CrashLoopBackOff means the container is repeatedly failing.

* Check logs
* Describe pod
* Verify env variables
* Check dependencies
* Fix root cause

---

## 3. Application slow

If app is slow, I check performance bottlenecks.

* CPU, memory, disk usage
* Application logs
* DB performance
* Network latency
* Scale if needed

---

## 4. Pod stuck in Pending

Pending means scheduler can’t place pod.

* Resource shortage
* Node issues
* PVC problems
* Taints/tolerations
* Scheduler logs

---

## 5. Internal works but external not

This is usually exposure issue.

* Service type
* Ingress config
* Security groups
* DNS mapping

---

## 6. Inter-service communication fails

This is mostly networking issue.

* Service DNS
* Ports
* Network policies
* Logs

---

## 7. Docker container running but app not responding

Container is up but app isn’t reachable.

* Check logs
* Verify port binding
* Check process
* Curl inside container

---

## 8. EC2 CPU 100%

High CPU impacts app availability.

* Identify process
* Check logs
* Restart if needed
* Scale instances
* Optimize app

---

## 9. Jenkins push to ECR fails

This is usually auth issue.

* IAM permissions
* ECR login
* Repo existence
* Network

---

## 10. RDS slow

Database slowness impacts app.

* Slow queries
* CPU/memory
* Connections
* Indexing
* Scaling

---

## 11. No logs but app down

Then I check infra level.

* Monitoring tools
* CPU/memory
* Network
* Enable logging

---

## 12. Intermittent issue

These require pattern analysis.

* Logs over time
* Traffic spikes
* Network issues
* Resource usage

---

## 13. CPU normal but slow

Then bottleneck is elsewhere.

* DB latency
* Memory issues
* Network
* Thread blocking

---

## 14. Zero downtime deployment

Used to avoid service interruption.

* Rolling deployment
* Blue-green
* Canary
* Health checks

---

## 15. Traffic spikes

Handled using scaling and optimization.

* Auto Scaling / HPA
* Caching (Redis)
* CDN
* Rate limiting
* Load testing

---

## 16. Memory leak

Requires deep analysis.

* Monitor memory
* Heap dump
* Profiling tools
* GC logs
* Fix retention
Memory leak is when an application keeps consuming memory without releasing it, causing usage to grow over time and eventually crash.
I will monitor memory usage trend, check logs and OOMKilled events, and fix by optimizing code or increasing limits temporarily.
---

## 17. ALB 502 error

Indicates backend failure.

* Target health
* Port mismatch
* Logs
* Timeout
* Service response

---

## 18. ECS unhealthy targets

Health/config issue.

* Health check path
* Port mapping
* Logs
* App binding
* Security groups

---

## 19. ALB vs Target Group vs Listener

They handle traffic routing.

* ALB: entry point
* Listener: port + rules
* Target Group: backend + health

---

## 20. ALB health check

Ensures backend availability.

* Path & port
* Interval/timeout
* Thresholds
* Routes to healthy only

---

## 21. Wrong health check path

Causes all targets to fail.

* Targets unhealthy
* No routing
* 503 error

---

## 22. Security Group vs NACL

Both control network access.

* SG: instance level, stateful, allow only
* NACL: subnet level, stateless, allow/deny

---

## 23. ASG with ALB

Ensures scaling and availability.

* Launch instances
* Register to target group
* Scaling policies
* Replace unhealthy

---

## 24. EC2 fails in ASG

ASG maintains desired state.

* Detect failure
* Terminate instance
* Launch new instance

---

## 25. ECS Task vs Task Definition vs Service

Defines ECS structure.

* Task Definition: blueprint
* Task: running container
* Service: maintains tasks

---

## 26. ECS self-healing

Ensures availability.

* Monitors tasks
* Replaces failed
* Maintains count

---

## 27. EC2 vs Fargate

Execution models differ.

* EC2: manage servers
* Fargate: serverless

---

## 28. Target type

Defines routing type.

* Instance: EC2
* IP: container/pod

---

## 29. ALB → ECS routing

Traffic flow.

* ALB → Target Group → Tasks

---

## 30. Wrong container port

Breaks routing.

* Health check fails
* Targets unhealthy
* No traffic

---

## 31. 502 vs 503

Different failure types.

* 502: backend error
* 503: no healthy targets

---

## 32. Debug ALB ↔ EC2 network

Check connectivity.

* Security groups
* NACL
* Port listening
* Curl test

---

## 33. Reverse proxy

Handles backend routing.

* Server forwards requests
* Example: ALB, Nginx

---

## 34. Forward proxy

Client-side proxy.

* Client → proxy → internet
* Used for filtering

---

## 35. Internal works not external

Usually exposure issue.

* Security groups
* ALB/Ingress
* DNS
* Firewall

---
