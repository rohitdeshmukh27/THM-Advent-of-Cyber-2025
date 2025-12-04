# 🎄 TryHackMe – Splunk Basics: Did You SIEM?

---

# **Task 1 — Introduction**

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

The entire lab is explained properly on the original website.
Here are just all the commands used and their explanations.

https://tryhackme.com/room/splunkforloganalysis-aoc2025-x8fj2k4rqp

## **🔹 Basic Exploration**

### **1. Show all logs**

```
index=main
```

- Displays all ingested logs.

### **2. Show only web_traffic logs**

```
index=main sourcetype=web_traffic
```

- Filters logs to the custom sourcetype _web_traffic_.

---

## **🔹 Timeline Visualization**

### **3. Count events per day**

```
index=main sourcetype=web_traffic | timechart span=1d count
```

- Shows number of logs per day.

### **4. Reverse sort by count**

```
index=main sourcetype=web_traffic | timechart span=1d count | sort by count | reverse
```

- Displays days with highest logs first.

---

## **🔹 Detect Suspicious Patterns**

### **5. Exclude legitimate browsers**

```
index=main sourcetype=web_traffic user_agent!=*Mozilla* user_agent!=*Chrome* user_agent!=*Safari* user_agent!=*Firefox*
```

- Shows only suspicious user agents.

### **6. Top attacker IPs**

```
sourcetype=web_traffic user_agent!=*Mozilla* user_agent!=*Chrome* user_agent!=*Safari* user_agent!=*Firefox*
| stats count by client_ip | sort -count | head 5
```

- Lists top suspicious IPs.

---

## **🔹 Reconnaissance Phase**

### **7. Detect probing attempts**

```
sourcetype=web_traffic client_ip="<REDACTED>" AND path IN ("/.env", "/*phpinfo*", "/.git*")
| table _time, path, user_agent, status
```

- Shows attempts to access config files.

---

## **🔹 Enumeration**

### **8. Detect path traversal & redirects**

```
sourcetype=web_traffic client_ip="<REDACTED>" AND path="*..*" OR path="*redirect*"
```

- Shows traversal or redirect attacks.

### **9. Count traversal attempts**

```
sourcetype=web_traffic client_ip="<REDACTED>" AND path="*..\/..\/*" OR path="*redirect*"
| stats count by path
```

- Shows number of attempts per path.

---

## **🔹 SQL Injection**

### **10. Detect SQLmap / Havij**

```
sourcetype=web_traffic client_ip="<REDACTED>" AND user_agent IN ("*sqlmap*", "*Havij*")
| table _time, path, status
```

- Detects automated SQL injection tools.

---

## **🔹 Exfiltration Attempts**

### **11. Download attempts for backup/log files**

```
sourcetype=web_traffic client_ip="<REDACTED>" AND path IN ("*backup.zip*", "*logs.tar.gz*")
| table _time, path, user_agent
```

- Shows exfiltration of large files.

---

## **🔹 Ransomware / RCE**

### **12. Detect webshell + ransomware execution**

```
sourcetype=web_traffic client_ip="<REDACTED>" AND path IN ("*bunnylock.bin*", "*shell.php?cmd=*")
| table _time, path, user_agent, status
```

- Confirms webshell and ransomware execution.

---

## **🔹 Firewall Correlation (C2 Communication)**

### **13. Detect outbound connection to attacker**

```
sourcetype=firewall_logs src_ip="10.10.1.5" AND dest_ip="<REDACTED>" AND action="ALLOWED"
| table _time, action, protocol, src_ip, dest_ip, dest_port, reason
```

- Shows server communicating with attacker’s C2.

---

## **🔹 Data Exfiltration Volume**

### **14. Sum bytes sent to attacker**

```
sourcetype=firewall_logs src_ip="10.10.1.5" AND dest_ip="<REDACTED>" AND action="ALLOWED"
| stats sum(bytes_transferred) by src_ip
```

- Shows total data exfiltrated.

---

# **Answers**

---

**Question: What is the attacker IP found attacking and compromising the web server?**

To find this, search using `index=main sourcetype=web_traffic` and check the **client_ip** field.
The IP with the highest event count is the attacker.

<details>
  <summary>Click to reveal the answer</summary>

**The answer:**
198.51.100.55

</details>

---

**Question: Which day was the peak traffic in the logs? (Format: YYYY-MM-DD)**

Run
`index=main sourcetype=web_traffic | timechart span=1d count`
Then check the **Visualization** tab.
The day with the highest spike is the answer.

<details>
  <summary>Click to reveal the answer</summary>

**The answer:**
2025-10-12

</details>

---

**Question: What is the count of Havij user_agent events found in the logs?**

Run:
`sourcetype=web_traffic client_ip="<REDACTED>" AND user_agent IN ("*sqlmap*", "*Havij*") | table _time, path, status`
Then check the **user_agent** field on the left.
Look for the entry **Havij/1.17 (Automated SQL Injection)** – the count shown is the answer.

<details>
  <summary>Click to reveal the answer</summary>

**The answer:**
993

</details>

---

**Question: How many path traversal attempts to access sensitive files on the server were observed?**

Run:
`sourcetype=web_traffic client_ip="198.51.100.55" AND path="*..*" OR path="*redirect*"`
Then check the **path** field.
The count for the request `/download?file=../../etc/passwd` (a sensitive file) is the answer.

<details>
  <summary>Click to reveal the answer</summary>

**The answer:**
658

</details>

---

**Question: Examine the firewall logs. How many bytes were transferred to the C2 server IP from the compromised web server?**

Run:
`sourcetype=firewall_logs src_ip="10.10.1.5" AND dest_ip="198.51.100.55" AND action="ALLOWED" | stats sum(bytes_transferred) by src_ip`
The result shows the number of bytes exfiltrated.

<details>
  <summary>Click to reveal the answer</summary>

**The answer:**
126167

</details>

---
