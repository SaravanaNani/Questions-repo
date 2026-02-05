### How can you build the above Docker image faster?
  
    FROM python:latest 
    WORKDIR /app 
    COPY . /app 
    RUN apt-get update && \ apt-get install -y --no-install recommends libpq-dev gcc &&
       \ pip install --upgrade pip && \ pip install -r requirements.txt && 
       \ apt-get remove -y gcc && 
       \ rm -rf /var/lib/apt/lists/* 
    
    ENV APP_HOME=/app 
    
    ENV PORT=8080 
    EXPOSE 8080 
    ENTRYPOINT ["python"] 
    CMD ["manage.py", "runserver", "0.0.0.0:8080"]

We speed up Docker builds by using lightweight base images, 
copying dependency files first to leverage layer caching, installing dependencies separately, copying application code later, 
and using a .dockerignore file to reduce build context.


### How does the TLS/SSL handshake work?
    🔹 Phase 1: TLS HANDSHAKE (Key Exchange Phase)
    
    Your sentence 👇
    
    Client verifies certificate, creates a session key, encrypts it with server’s public key, and the server decrypts it using its private key.
    
    ✅ YES — this is part of the TLS handshake.
    
    What happens here:
    
    Client verifies server identity
    
    Client and server agree on security
    
    Client securely sends the session key
    
    No application data is exchanged yet
    
    👉 Purpose of handshake:
    
    Safely agree on a shared secret (session key)
    
    🔹 Phase 2: SECURE DATA COMMUNICATION (After Handshake)
    
    Your sentence 👇
    
    Client and server communicate using a shared session key. All requests and responses are encrypted with this key.
    
    ✅ YES — this happens AFTER the handshake.
    
    What happens here:
    
    Same session key is used
    
    Data flows both directions
    
    Everything is encrypted
    
    No public/private keys involved anymore
    
    👉 Purpose:
    
    Secure, fast data transfer
    

🔐 What is TLS Termination?

TLS termination means decrypting HTTPS traffic at the Load Balancer or Ingress, not at the application.

### A VM is unable to access an API internally. How would you troubleshoot and resolve this?

M → Internal API Troubleshooting (Short)

1️⃣ Check basic connectivity

    ping <API_IP>
    curl http://<API_IP>:<PORT>

2️⃣ Check DNS resolution (if using hostname)

    nslookup api.internal
    dig api.internal

3️⃣ Check port reachability

    nc -zv <API_IP> <PORT>
    telnet <API_IP> <PORT>

4️⃣ Verify Security Group / Firewall

✔ VM outbound rules allow traffic
✔ API inbound rules allow VM subnet / SG
✔ Correct port open (80/443/8080)

5️⃣ Check routing

✔ Same VPC / network
✔ Route tables correct
✔ No missing routes

6️⃣ Check API service is running & listening

    systemctl status <service>
    netstat -tulnp | grep <PORT>


✔ Must be bound to 0.0.0.0, not 127.0.0.1

7️⃣ Check OS firewall on API VM

    iptables -L
    ufw status

8️⃣ Check TLS / HTTPS (if applicable)
        
    curl -vk https://api.internal
    openssl s_client -connect api.internal:443
    
  MEMPoint:
  
    Ping fails → Network issue
    Connection refused → Service / port issue
    HTTPS fails → TLS / certificate issue


iptables is a Linux firewall tool that controls network traffic at the OS level by allowing or blocking packets based on rules.
OS-level firewalls like UFW can block traffic even if cloud security groups allow it, so they must be checked during connectivity issues.

    🧠 Memory tip
    
    UFW = easy firewall
    iptables = actual firewall engine



### How does a NAT Gateway work end to end? If many private instances send requests, why does the destination see only one public IP?

    A NAT Gateway allows private instances to access the internet by translating their private IPs to a single public IP. 
    The destination sees only the NAT’s public IP because all traffic is source-NATed using port translation.



### Explain the end-to-end flow from client → Ingress → Service at the kube-proxy level.

    Client traffic enters through Ingress, which routes requests based on host and path rules to a Service. 
    kube-proxy then forwards traffic to one of the backend Pods using iptables or IPVS rules.


### Why does DNS propagation take time after a DNS change? Apart from TTL, what other factors are involved?


DNS propagation takes time due to caching at multiple levels such as browsers, OS, ISPs, and recursive resolvers.
Apart from TTL, ISP behavior, negative caching, DNS provider sync delays, and geo-distribution also affect propagation time.

🧠 Memory tip
TTL controls cache duration, but caches exist everywhere.



### During a Terraform apply, the node goes down and no state file is created, leaving half the resources created. How do you resolve this?

    If Terraform apply fails and resources are partially created, I inspect existing resources, 
    import them into Terraform state using terraform import, and then re-run plan and apply.
    Using a remote backend with state locking prevents this issue.

    Lifecycle blocks are useful to prevent unwanted changes or deletions, but they cannot recover from missing Terraform state.
    In such cases, importing existing resources into the state is the correct approach.

### As a DevOps engineer, what Kubernetes metrics do you monitor using Prometheus?


As a DevOps engineer, what Kubernetes metrics do you monitor using Prometheus?

    1️⃣ Node-level metrics (infrastructure health)
    
    Collected via Node Exporter
    
    Monitor:
    
    CPU usage
    
    Memory usage
    
    Disk usage
    
    Network I/O
    
    Node availability (up/down)
    
    Why?
    👉 To detect overloaded or failing nodes
    
    2️⃣ Pod-level metrics (application health)
    
    Collected via kube-state-metrics + cAdvisor
    
    Monitor:
    
    Pod CPU & memory usage
    
    Pod restarts
    
    OOMKilled events
    
    Pod status (Running / Pending / CrashLoopBackOff)
    
    Why?
    👉 To catch crashing or unhealthy pods
    
    3️⃣ Container-level metrics
    
    Monitor:
    
    Container CPU throttling
    
    Container memory limits
    
    Container restarts
    
    Why?
    👉 To detect resource limit issues
    
    4️⃣ Service & workload metrics
    
    Monitor:
    
    Request count
    
    Request latency
    
    Error rates (4xx / 5xx)
    
    Why?
    👉 To ensure application performance
    
    5️⃣ Cluster-level metrics
    
    Monitor:
    
    Total cluster CPU/memory
    
    Scheduler failures
    
    Pending pods
    
    Why?
    👉 Capacity planning and scaling
    
    6️⃣ Control-plane metrics (important in prod)
    
    Monitor:
    
    API server latency
    
    etcd health
    
    Controller manager failures
    
    Why?
    👉 Cluster stability


### ALB vs NLB – explain the differences.

    NLB forwards traffic based on IP and port and is optimized for high-performance, low-latency workloads.
    ALB routes traffic based on application-level information like URL paths and hostnames.


### Proxy vs API Gateway – what is the difference?

    A proxy simply forwards traffic between clients and servers, while an API Gateway acts as a centralized entry point that manages authentication, rate limiting, routing, and monitoring for APIs.
    
    An API Gateway handles authentication using API keys, tokens, or IAM, enforces rate limiting to protect backend services, and supports API versioning to allow backward compatibility.

### How does Docker use cgroups and namespaces?

    Namespaces isolate containers so they cannot see or affect other containers or the host.
    cgroups control:
    
    “How much CPU, memory, disk, and network a container can use”
    cgroups limit and control how much system resources a container can consume.


### CPU usage is constantly high. How do you troubleshoot it?




### A service needs shared storage. Would you use EBS or EFS, and why?

If a service requires shared storage across multiple instances, 
I would use EFS because it supports concurrent access and scales automatically. 
If the service runs on a single instance and needs high performance, I would use EBS


CPU usage is constantly high. How do you troubleshoot it?

A service needs shared storage. Would you use EBS or EFS, and why?
