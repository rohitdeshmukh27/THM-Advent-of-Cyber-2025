# 🎄 TryHackMe – Splunk Basics: Did You SIEM?

## **Day 3 — Structured Notes (Markdown)**

---

## **Task 1 — Introduction**

### **📝 The Story**

- Days before Christmas, TBFC (The Best Festival Company) detects a **ransomware alert**.
- A ransom message appears from **King Malhare**, ruler of HopSec Island.
- He wants to replace Christmas with “**EAST-mas**” and sends **Bandit Bunnies** to infiltrate TBFC systems.
- With McSkidy missing, the TBFC SOC uses **Splunk SIEM** to investigate how the ransomware entered.

---

### **🎯 Learning Objectives**

- Understand Splunk log ingestion & searching
- Create/interpret field extractions
- Use **Search Processing Language (SPL)**
- Perform an attack investigation inside Splunk

---

### **🖥️ Connecting to Splunk**

- Start the Machine → wait 2–3 minutes
- Access Splunk at:
  `https://LAB_WEB_URL.p.thmlabs.com`
- If you see a **502**, wait longer; Splunk takes time to initialize.

---

# **Task 2 — Log Analysis with Splunk**

---

# 🕵️ Part 1 — Exploring Logs

### **Open Search & Reporting**

Left panel → **Search & Reporting**.

### **Show all logs**

```
index=main
```

- **index=main** → search inside the main index
- Set **Time Range: All time**

### **Two sourcetypes found:**

- **web_traffic** → HTTP/web events (client_ip, path, user_agent, status, etc.)
- **firewall_logs** → Allowed/block traffic logs (src_ip, dest_ip, port, bytes, etc.)

---

# 🕵️ Part 2 — Initial Triage

### **View only web traffic**

```
index=main sourcetype=web_traffic
```

### Splunk Interface Breakdown

- **Search Bar** → run SPL queries
- **Timeline Chart** → event volume over time
- **Fields Sidebar**

  - _Selected fields_ → currently shown
  - _Interesting fields_ → auto-extracted (user_agent, path, client_ip)

- **Event Viewer** → raw log + parsed fields

---

# 📊 Part 3 — Visualizing Logs

### View events per day

```
index=main sourcetype=web_traffic | timechart span=1d count
```

**Explanation:**

- `timechart` → visual/time-based charts
- `span=1d` → group by 1 day
- `count` → count events per day

### Sort results (descending)

```
index=main sourcetype=web_traffic
| timechart span=1d count
| sort by count
| reverse
```

Peak log volume day = **2025-10-12**

---

# 🐰 Part 4 — Anomaly Detection

### Analyze suspicious fields:

#### **1. user_agent**

- Shows many **non-browser tools** like curl, wget, sqlmap, Havij.

#### **2. client_ip**

- One IP dominates → likely attacker.

#### **3. path**

- Many suspicious requests:

  - `/.env`
  - `/phpinfo.php`
  - `../../etc/passwd`
  - `/shell.php?cmd=`

---

# 🧹 Part 5 — Filter Benign Traffic

### Remove common browser user agents

```
index=main sourcetype=web_traffic
user_agent!=*Mozilla* user_agent!=*Chrome* user_agent!=*Safari* user_agent!=*Firefox*
```

**Why?**
Attackers use tools, not browsers → this isolates malicious traffic.

### Identify attacker IP

```
sourcetype=web_traffic
user_agent!=*Mozilla* user_agent!=*Chrome* user_agent!=*Safari* user_agent!=*Firefox*
| stats count by client_ip
| sort -count
| head 5
```

Attacker IP found → **198.51.100.55**

---

# 🔍 Part 6 — Tracing the Attack Chain

Replace `<REDACTED>` with **198.51.100.55**.

---

## **1. Reconnaissance**

### Probing for config files

```
sourcetype=web_traffic client_ip="198.51.100.55"
AND path IN ("/.env", "/*phpinfo*", "/.git*")
| table _time, path, user_agent, status
```

**Status Codes Meaning:**

- **404** → not found
- **403** → forbidden
- **401** → unauthorized

---

## **2. Enumeration (Path Traversal & Redirects)**

### Initial check

```
sourcetype=web_traffic client_ip="198.51.100.55" AND path="*..*" OR path="*redirect*"
```

### More accurate (escaping ../ )

```
sourcetype=web_traffic client_ip="198.51.100.55"
AND path="*..\/..\/*" OR path="*redirect*"
| stats count by path
```

**Total path traversal attempts:** **658**

---

## **3. SQL Injection Attempts**

```
sourcetype=web_traffic client_ip="198.51.100.55"
AND user_agent IN ("*sqlmap*", "*Havij*")
| table _time, path, status
```

**Tool indicators:**

- **sqlmap** → automated SQLi tool
- **Havij** → GUI SQLi tool

Havij count = **993**

---

## **4. Exfiltration Attempts**

```
sourcetype=web_traffic client_ip="198.51.100.55"
AND path IN ("*backup.zip*", "*logs.tar.gz*")
| table _time, path, user_agent
```

Shows download attempts of large compressed files.

---

## **5. Ransomware Staging / Webshell**

### Find ransomware & webshell execution

```
sourcetype=web_traffic client_ip="198.51.100.55"
AND path IN ("*bunnylock.bin*", "*shell.php?cmd=*")
| table _time, path, user_agent, status
```

Confirms:

- **shell.php** → active webshell
- **cmd=./bunnylock.bin** → ransomware executed

---

# 📡 Part 7 — C2 (Command & Control) Traffic

### Check outbound connections from compromised server

```
sourcetype=firewall_logs
src_ip="10.10.1.5"
AND dest_ip="198.51.100.55"
AND action="ALLOWED"
| table _time, action, protocol, src_ip, dest_ip, dest_port, reason
```

**C2 Confirmed:**

- **action=ALLOWED**
- **reason=C2_CONTACT**

---

# 📦 Part 8 — Volume of Exfiltrated Data

```
sourcetype=firewall_logs
src_ip="10.10.1.5"
AND dest_ip="198.51.100.55"
AND action="ALLOWED"
| stats sum(bytes_transferred) by src_ip
```

Total data transferred = **126167 bytes**

---

# 🧠 Conclusion (Attack Summary)

### **Attacker IP:** 198.51.100.55

### **Peak activity day:** 2025-10-12

### **Attack Flow:**

| Stage                 | Evidence                              |
| --------------------- | ------------------------------------- |
| **Recon**             | Probing for `.env`, `.git`, `phpinfo` |
| **Enumeration**       | Path traversal: `../../etc/passwd`    |
| **SQLi**              | sqlmap & Havij user_agents            |
| **Webshell**          | `/shell.php?cmd=`                     |
| **Payload Execution** | `bunnylock.bin` ransomware            |
| **C2 Communication**  | Outbound to attacker IP               |
| **Exfiltration**      | 126167 bytes sent                     |

---

# ✔️ Answers

| Question                | Answer            |
| ----------------------- | ----------------- |
| Attacker IP             | **198.51.100.55** |
| Peak log day            | **2025-10-12**    |
| Havij user_agent count  | **993**           |
| Path traversal attempts | **658**           |
| Bytes sent to C2        | **126167**        |

---
