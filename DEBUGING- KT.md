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
