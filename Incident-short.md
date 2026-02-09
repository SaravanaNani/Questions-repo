# Incident Management – Linux, Docker, Kubernetes & DevOps

---

## 🟥 Linux Incident 1: Server is slow, CPU is low but load average is high

**Why it happens:**  
Processes are blocked waiting for disk or network I/O, not CPU. They enter D (uninterruptible sleep) state.

**How to resolve:**  
Identify I/O bottleneck, reduce disk/network latency, add caching, improve storage performance.

**Cmd:**  
top  
uptime  
iostat  
vmstat  

---

## 🟥 Linux Incident 2: One process uses 100% CPU on multi-core system

**Why it happens:**  
A single thread/process is overworking, causing bottleneck even though other CPUs are free.

**How to resolve:**  
Identify hot thread, restart process, optimize code, or scale out.

**Cmd:**  
top -H -p <PID>  
ps -Lp <PID> -o pid,tid,%cpu,cmd  

---

## 🟥 Linux Incident 3: App killed even though free memory exists (OOM)

**Why it happens:**  
Process exceeds cgroup or container memory limit, triggering OOM killer.

**How to resolve:**  
Increase memory limits or fix memory leaks.

**Cmd:**  
dmesg | grep -i oom  

---

## 🟥 Linux Incident 4: Application crashes repeatedly

**Why it happens:**  
Restart managed by systemd, cron, Docker, or Kubernetes controller.

**How to resolve:**  
Identify restart source and fix underlying application/config issue.

**Cmd:**  
systemctl status <service>  
journalctl -u <service>  

---

## 🟥 Linux Incident 5: Server has free RAM but apps are slow

**Why it happens:**  
Linux uses swap (disk-based), causing slowness despite available RAM.

**How to resolve:**  
Reduce swap usage, add RAM, fix memory leaks.

**Cmd:**  
free -h  
vmstat 1 5  
swapon -s  

---

## 🟥 Linux Incident 6: Too many open files error

**Why it happens:**  
File descriptor limit (`ulimit -n`) is too low.

**How to resolve:**  
Increase file descriptor limits.

**Cmd:**  
ulimit -n  
lsof | wc -l  

---

## 🟥 Linux Incident 7: Disk is full, application stopped

**Why it happens:**  
Logs, temp files, or Docker artifacts consume disk.

**How to resolve:**  
Clean logs, temp files, Docker images/volumes.

**Cmd:**  
df -h  
du -sh /var/log/*  
docker system df  

---

## 🟥 Linux Incident 8: App is running but users say it’s slow

**Why it happens:**  
CPU waits for disk/network I/O, system enters D state.

**How to resolve:**  
Reduce I/O wait, improve storage/network, add caching, scale.

**Cmd:**  
top  
iostat  
vmstat  

---

## 🟥 Linux Incident 9: Server not reachable via SSH

**Why it happens:**  
Network issue, disk full, CPU/memory exhaustion, firewall blocking port 22.

**How to resolve:**  
Free disk, reduce load, restart sshd, allow port 22.

**Cmd:**  
df -h  
top  
iptables -L  
systemctl status sshd  

---

## 🟥 Linux Incident 10: Zombie processes increasing

**Why it happens:**  
Parent process does not reap child processes.

**How to resolve:**  
Restart parent process.

**Cmd:**  
ps aux | grep Z  
pstree -p  

---

## 🟥 Linux Incident 11: Network suspected, app latency high

**Why it happens:**  
High latency, packet loss, or routing issues.

**How to resolve:**  
Identify latency source and fix network path.

**Cmd:**  
ping  
traceroute  
ss  

---

## 🟥 Linux Incident 12: Service won’t start

**Why it happens:**  
Configuration errors or missing dependencies.

**How to resolve:**  
Check service logs and fix configuration.

**Cmd:**  
systemctl status <service>  
journalctl -u <service>  

---

## 🟥 Docker Incident 1: Container crashing repeatedly

**Why it happens:**  
App crash, wrong entrypoint, missing environment variables.

**How to resolve:**  
Fix app/config and redeploy container.

**Cmd:**  
docker ps -a  
docker logs <container>  

---

## 🟥 Docker Incident 2: Container OOMKilled

**Why it happens:**  
Container exceeds memory limit enforced by cgroups.

**How to resolve:**  
Increase memory limit or optimize application.

**Cmd:**  
docker inspect <container>  
dmesg | grep oom  

---

## 🟥 Docker Incident 3: High CPU usage in container

**Why it happens:**  
No CPU limit, infinite loop, or traffic spike.

**How to resolve:**  
Set CPU limits or scale containers.

**Cmd:**  
docker stats  
top  

---

## 🟥 Kubernetes Incident 1: Pod CrashLoopBackOff

**Why it happens:**  
App crash or wrong configuration.

**How to resolve:**  
Fix config/app and redeploy pod.

**Cmd:**  
kubectl logs <pod>  
kubectl describe pod <pod>  

---

## 🟥 Kubernetes Incident 2: Pod OOMKilled

**Why it happens:**  
Pod exceeds memory limit.

**How to resolve:**  
Increase memory limit or optimize app.

**Cmd:**  
kubectl describe pod <pod>  

---

## 🟥 Kubernetes Incident 3: Pod stuck in Pending

**Why it happens:**  
No node resources or PVC pending.

**How to resolve:**  
Add nodes or fix resource requests.

**Cmd:**  
kubectl describe pod <pod>  

---

## 🟥 Kubernetes Incident 4: Service not reachable

**Why it happens:**  
Selector mismatch or pod not ready.

**How to resolve:**  
Fix labels and readiness probes.

**Cmd:**  
kubectl get svc  
kubectl get endpoints  

---

## 🟥 Kubernetes Incident 5: Ingress / LoadBalancer not working

**Why it happens:**  
Wrong ingress rules or TLS issues.

**How to resolve:**  
Fix host/path rules and certificates.

**Cmd:**  
kubectl describe ingress  

---

## 🟥 DevOps Incident: Jenkins Pipeline Failure

**Why it happens:**  
Dependency issue, agent down, permission/plugin error.

**How to resolve:**  
Fix pipeline, restart agent, re-run build.

**Cmd:**  
Jenkins console logs  
systemctl status jenkins  

---

## 🟥 DevOps Incident: Terraform Apply Failure

**Why it happens:**  
IAM permission issue or validation error.

**How to resolve:**  
Fix IAM and re-apply Terraform.

**Cmd:**  
terraform plan  
terraform apply  

---

## 🟥 DevOps Incident: Partial Terraform Resources Created

**Why it happens:**  
Apply interrupted or node crash.

**How to resolve:**  
Import resources and continue apply.

**Cmd:**  
terraform import  
terraform state list  

---

## 🟥 Incident: VM Unable to Access Internal API

**Why it happens:**  
Network issue, DNS issue, port blocked, service not listening, firewall/TLS issue.

**How to resolve:**  
Check connectivity, DNS, ports, firewall, routing, service status, and TLS.

**Cmd:**  
ping <API_IP>  
curl http://<API_IP>:<PORT>  
nslookup api.internal  
dig api.internal  
nc -zv <API_IP> <PORT>  
systemctl status <service>  
netstat -tulnp | grep <PORT>  
iptables -L  
ufw status  
curl -vk https://api.internal  

**Memory Points:**  
Ping fails → Network issue  
Connection refused → Service/port issue  
HTTPS fails → TLS/certificate issue  

**Note:**  
iptables is the Linux firewall engine.  
UFW is a user-friendly wrapper over iptables.  
OS firewalls can block traffic even if cloud security groups allow it.

---

## 🧠 Bottleneck Summary

CPU bottleneck → High CPU %, low I/O wait  
Memory bottleneck → High swap, OOM kills  
Disk I/O bottleneck → High I/O wait, D-state processes  
Network bottleneck → High latency, packet drops  

---
