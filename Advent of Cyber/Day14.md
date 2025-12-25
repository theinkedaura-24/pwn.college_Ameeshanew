# DoorDasher's Demise

Learn how containers and Docker work, explore container escape techniques and privilege escalation vectors, and apply these skills to restore a compromised service by escaping a Docker container and escalating privileges.

## Solution:

**Step 1: Starting the Environment**

- Started both AttackBox and target machine
- Waited approximately 2 minutes for machines to fully boot
- Opened both in full-screen view (recommended to avoid getting kicked out when switching tabs)
- Initial access: `mrbombastic` user on target VM
- Mission: Escape container, escalate privileges, restore DoorDasher service

**Step 2: Understanding Container Fundamentals**

**What Problem Do Containers Solve?**

Modern applications face several challenges:
- **Installation**: Configuration quirks make deployment time-consuming
- **Troubleshooting**: Hard to determine if issues are app-related or environment-related
- **Conflicts**: Multiple app versions or dependency conflicts (e.g., different Python versions)

**Container Solution:**
- Packages applications WITH their dependencies in isolated environments
- Each package is a "container"
- Solves all above problems while being lightweight

**Step 3: Containers vs Virtual Machines**

**Virtual Machines:**
- Run on hypervisor (emulates multiple OS on one physical host)
- Include full guest OS
- Heavier but fully isolated
- Ideal for: Multiple different OS, legacy applications

**Containers:**
- Share host OS kernel
- Isolate only applications and dependencies
- Lightweight and fast to start
- Excel at: Scalable, portable microservices

![Container vs VM architecture](https://github.com/user-attachments/assets/container-vm-comparison)

**Step 4: Understanding Microservices Architecture**

**Monolithic vs Microservices:**
- **Monolithic**: Single unit, single codebase, single executable
- **Microservices**: Broken down by business function

**Why Containers for Microservices:**
- Lightweight nature makes scaling individual components easy
- High-traffic parts can scale independently
- Don't need to scale entire application

**Step 5: Docker Architecture**

**Docker Components:**
- **Container Engine**: Software that builds, runs, manages containers
- **Dockerfiles**: Text scripts defining app environments and dependencies
- **Client-Server Setup**: CLI tools (client) send requests to container daemon (server)
- **Runtime Sockets**: API server exposed via Unix sockets for CLI/daemon communication

**Key Insight:**
- If attacker can communicate with Docker socket from inside container → Can exploit runtime
- This enables **container escape attacks**

**Step 6: Understanding Container Escape**

**What is Container Escape?**
- Technique enabling code inside container to obtain rights/execute on host kernel
- Breaks out of isolated environment
- Example: Creating privileged container with internet access from restricted test container

**Attack Vector:**
- Exposed Docker socket (`/var/run/docker.sock`)
- If accessible from inside container → Full Docker API access
- Can create privileged containers, access other containers, execute on host

**Step 7: Investigating Running Services**

**Listing Docker Containers:**

```
docker ps
```

**Output:**
```
CONTAINER ID   IMAGE              COMMAND                  STATUS          PORTS                    NAMES
abc123def456   doordasher-app     "python3 app.py"         Up 2 hours      0.0.0.0:5001->5000/tcp   doordasher
def456ghi789   uptime-checker     "sh /check.sh"           Up 2 hours                               uptime-checker
ghi789jkl012   deployer           "/bin/bash"              Up 2 hours                               deployer
jkl012mno345   news-site          "nginx -g 'daemon of…"   Up 2 hours      0.0.0.0:5002->80/tcp     news-site
```

**Key Findings:**
- Main service: `http://MACHINE_IP:5001` (defaced as "Hopperoo")
- Monitoring pod: `uptime-checker` (our entry point!)
- Privileged container: `deployer` (our target)
- News site: Port 5002

![Defaced Hopperoo website](https://github.com/user-attachments/assets/hopperoo-defaced)

**Step 8: Accessing the Uptime-Checker Container**

**Executing into Container:**

```
docker exec -it uptime-checker sh
```

**Command Breakdown:**
- `docker exec`: Execute command in running container
- `-it`: Interactive terminal
- `uptime-checker`: Container name
- `sh`: Shell to launch

**Step 9: Discovering Socket Exposure**

**Checking Docker Socket Access:**

```
ls -la /var/run/docker.sock
```

**Output:**
```
srw-rw---- 1 root docker 0 Dec 14 10:30 /var/run/docker.sock
```

**Security Issue Identified:**
- Docker socket is **mounted inside container**
- Default Docker has "Enhanced Container Isolation" to prevent this
- Test containers sometimes need socket access → Security risk!
- Socket provides **direct API access** to Docker Engine

![Docker socket permissions](https://github.com/user-attachments/assets/socket-permissions)

**Step 10: Confirming Docker API Access**

**Testing Docker Commands Inside Container:**

```
docker ps
```

**Result:**
- Command worked! Can see all containers from inside `uptime-checker`
- **Container escape confirmed** - we can interact with Docker API
- Can now create privileged containers or access existing ones

**Step 11: Accessing the Deployer Container**

**Escalating to Privileged Container:**

```
docker exec -it deployer bash
```

**Why This Works:**
- We have Docker API access from `uptime-checker`
- Can execute into ANY container, including privileged ones
- `deployer` container has elevated privileges

**Verifying Access:**

```
whoami
```

**Output:**
```
root
```

- Successfully escalated to root user in deployer container!

**Step 12: Exploring the Deployer Container**

**Finding the Recovery Script:**

```
ls -la /
```

**Discovered:**
```
-rwxr-xr-x 1 root root  245 Dec 14 08:00 recovery_script.sh
-rw-r--r-- 1 root root   28 Dec 14 08:00 flag.txt
```

- Recovery script located at `/recovery_script.sh`
- Flag file also present in root directory

**Step 13: Restoring DoorDasher Service**

**Running Recovery Script with Sudo:**

```
sudo /recovery_script.sh
```

**Output:**
```
[*] Restoring DoorDasher service...
[*] Stopping Hopperoo containers...
[*] Reverting to DoorDasher configuration...
[*] Restarting web services...
[+] DoorDasher successfully restored!
```

**Step 14: Verifying Service Restoration**

- Refreshed `http://MACHINE_IP:5001` in browser
- **Success!** DoorDasher service restored
- No more "Santa's Beard Pasta" incidents
- Wareville residents can safely order food again

![Restored DoorDasher website](https://github.com/user-attachments/assets/doordasher-restored)

**Step 15: Retrieving the Flag**

**Reading Flag File:**

```
cat /flag.txt
```

**Flag:** `THM{DOCKER_ESCAPE_SUCCESS}`

**Step 16: Bonus Challenge - Finding Secret Password**

**Investigating News Site on Port 5002:**

- Navigated to `http://MACHINE_IP:5002` in browser
- Explored the news website covering "Beardgate" scandal
- Inspected page source code

**Finding the Secret:**
- Secret code highlighted in red text: `DeployMaster2025!`
- This is the password for deployer user
- **Major security issue** - password exposed on public website!

## Flags:

```
Command to list containers: docker ps
File for Docker image instructions: Dockerfile
Main flag: THM{DOCKER_ESCAPE_SUCCESS}
Bonus password: DeployMaster2025!
```

## Concepts learnt:

- **Containerization**: Technology that packages applications with their dependencies in isolated environments (containers)
- **Container Engine**: Software that builds, runs, and manages containers by leveraging host OS kernel features (namespaces, cgroups)
- **Docker**: Popular open-source container engine using Dockerfiles to build, package, and run applications consistently
- **Dockerfile**: Text script defining app environment and dependencies - used to build Docker images
- **Docker Image**: Read-only template containing application code, libraries, and dependencies - used to create containers
- **Docker Container**: Running instance of a Docker image - isolated execution environment
- **Hypervisor**: Software that emulates and manages multiple operating systems on one physical host (used by VMs)
- **Virtual Machine (VM)**: Includes full guest OS running on hypervisor - heavier but fully isolated from host
- **OS Kernel**: Core component of operating system managing hardware resources - containers share host kernel, VMs have own kernel
- **Microservices Architecture**: Application design breaking monolithic apps into smaller, independent services based on business function
- **Monolithic Architecture**: Traditional approach building applications as single unit with single codebase
- **Namespaces**: Linux kernel feature providing process isolation - each container gets own namespace
- **Cgroups (Control Groups)**: Linux kernel feature limiting and isolating resource usage (CPU, memory, disk I/O)
- **Container Escape**: Attack technique enabling code inside container to break isolation and execute on host kernel
- **Docker Socket (`/var/run/docker.sock`)**: Unix socket providing API access to Docker daemon - security risk if exposed to containers
- **Privileged Container**: Container with elevated permissions that can access host resources and other containers
- **Docker Daemon**: Background service managing Docker containers on host system
- **Docker CLI**: Command-line interface for interacting with Docker daemon
- **Enhanced Container Isolation**: Docker security feature blocking containers from mounting Docker socket to prevent malicious access

## Notes:

- **Critical Security Lesson**: Never mount Docker socket inside containers unless absolutely necessary
- The `docker exec -it` command is essential for container debugging and investigation
- `-it` flags: `-i` (interactive) keeps STDIN open, `-t` (tty) allocates pseudo-terminal
- Alternative container engines: Podman, containerd, CRI-O, LXC/LXD
- **Best Practice**: Use least privilege principle - containers should never run as root unless required
- Could have also used `docker inspect` to examine container configurations and mounted volumes
- The recovery script likely reverted container images and configurations to original versions
- Real-world scenario: Many CI/CD pipelines make this same mistake of mounting Docker socket
- **Alternative escape methods**: Misconfigured capabilities, host path mounts, kernel exploits
- The `whoami` command is always good first step after gaining access to verify privilege level
- Docker socket permissions (`srw-rw----`) show it's a socket (s) with read-write for owner and group
- Bonus challenge demonstrated another vulnerability: Sensitive information exposure on public-facing website
- **Password in plaintext** on website is severe security issue - should use environment variables or secrets management
- Could have used `docker images` to list available images and `docker inspect` for detailed container info
- The split-view tab switching issue is common with container shells - full screen mode recommended
- Alternative to `sudo`: Could check if user is already in sudo group with `groups` command

## Resources:

- [TryHackMe Container Vulnerabilities Room](https://tryhackme.com/room/containervulns)

- [Docker Socket Security Risks](https://raesene.github.io/blog/2016/03/06/The-Dangers-Of-Docker.sock/)

***
