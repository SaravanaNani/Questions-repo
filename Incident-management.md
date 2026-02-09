# Linux: Incident Management

---

## 🟥 Incident 1: Server is slow, CPU is low but load average is high

High load with low CPU means processes are waiting on I/O, not CPU.

Processes are blocked on I/O, not CPU.  
- Disk I/O  
- N/w I/O  
- NFS / EBS latency  
- Processes in D state  

**cmd:**  
`top` shows low CPU usage, but `uptime` shows high load average.

-> Load average shows the average number of processes waiting to run or running on CPU over a period of time.

---

## 🟥 Incident 2: One process uses 100% CPU on multi-core system
System has 8 CPUs, but one app is slow and CPU is spiking.

How to resolve:  
One thread/process is overworking, so the app becomes slow even though other CPUs are free.

Identify the process/thread (`top`, `ps`), then restart it, optimize code, or scale out if traffic is high.

-> Identify which thread is consuming CPU.

**cmd:**  
```bash
top -H -p <PID>
ps -Lp <PID> -o pid,tid,%cpu,cmd
```

## 🟥 Incident 3: App killed even though free memory exists (OOM)

Logs show:
```
Out of memory: Kill process xxxx
```

-> OOM can occur due to cgroup limits even if host has free RAM.


## 🟥 Incident 4: Application crashes repeatedly

Service restarts automatically every few seconds.

-> Repeated restarts usually come from systemd, cronJobs, Docker, or Kubernetes controllers.


## 🟥 Incident 5: Server has free RAM but apps are slow

Why it happens:

Linux starts using swap, which is disk-based and slow, even when some RAM looks free.

cmd:

    free -h
    vmstat 1 5
    swapon -s


-> Reduce swap usage, add more RAM, fix memory leaks, or restart memory-heavy processes.


## 🟥 Incident 6: Too many open files error

App throws: Too many open files


-> File descriptor exhaustion causes connection failures.

Root cause: ulimit -n too low.

    ulimit -n
    lsof | wc -l


Fix: Increase limits in:

    /etc/security/limits.conf


## 🟥 Incident 7: Disk is full, app stopped working

-> Disk full incidents are often caused by logs or Docker artifacts.

Steps:
    
    df -h
    du -sh /*
    
-> Clear logs:

    du -sh /var/log/*


-> Remove temp files

-> Check Docker volumes: ```docker system df```


## 🟥 Incident 8: App is running but users say it’s slow

-> Always check CPU, memory, I/O, and network in that order.

Because:

    CPU is waiting for data
    Disk or network is slow
    
    Process goes into D (uninterruptible sleep) state

      
    CPU slow → scale CPU
    I/O slow → reduce waiting

When I/O is slow, processes wait for disk or network data and go into D state, making the system slow even if CPU is free. 
I resolve this by reducing disk usage, improving storage performance, adding caching, fixing network latency, or scaling the system.


## 🟥 Incident 9: Server not reachable via SSH

-> SSH issues are usually network, disk, or resource exhaustion.

Why it happens:

SSH service running: systemctl status sshd -> df -h

CPU/memory exhausted: SSH daemon can’t get resources --> top  

Disk full: SSH can’t write logs or temp files

Firewall rules: Port 22 blocked -> iptables -L

Network issue: Server not reachable


### How to resolve:

Free disk space, restart sshd, reduce load, and allow port 22 in firewall/Security Group.



## 🟥 Incident 10: Zombie processes increasing

Why it happens:
Parent process didn’t reap child.

Check:
```
ps aux | grep Z
pstree -p
```

Fix: Restart parent process.

-> Zombies consume PIDs and indicate broken parent processes.

## 🟥 Incident 11: Network suspected, app latency high

-> Network latency is validated using ping, traceroute, and socket analysis.


## 🟥 Incident 12: Service won’t start

-> systemctl and journalctl are primary tools for service failures.


## 3️⃣ What is a bottleneck? How CPU / Memory / Disk / Network differ


Bottleneck meaning:
A bottleneck is the one resource limiting overall performance.

    CPU bottleneck: High CPU %, low I/O wait
    
    Memory bottleneck: High swap usage, OOM kills
    
    Disk I/O bottleneck: High I/O wait, many D-state processes
    
    Network bottleneck: High latency, packet drops

