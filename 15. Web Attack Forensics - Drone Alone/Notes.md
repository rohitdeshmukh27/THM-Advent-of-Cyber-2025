# **Web Attack Forensics – Drone Alone**

---

## **Task 1 – Introduction**

### **Incident Summary**

- Drone scheduler web UI receiving **long HTTP requests**
- Requests contain **Base64-encoded payloads**
- Apache spawns **unexpected OS processes**
- Some payloads trigger **remote command execution**
- Goal:

  - Detect malicious web requests
  - Correlate Apache logs with Sysmon
  - Decode attacker payloads
  - Reconstruct attack chain

---

## **Learning Objectives**

- Detect command injection via Apache logs
- Identify post-exploitation via Sysmon
- Decode Base64 PowerShell payloads
- Correlate web + host telemetry in Splunk

---

## **Task 2 – Web Attack Forensics**

---

## **Splunk Access**

- URL: `http://MACHINE_IP:8000`
- Credentials:

  - **Username:** Blue
  - **Password:** Pass1234

- Set time range to **Last 7 days / All time**

---

## **Step 1 – Detect Suspicious Web Requests**

### **Goal**

Identify command injection attempts via HTTP parameters.

### **Splunk Query**

```spl
index=windows_apache_access (cmd.exe OR powershell OR "powershell.exe" OR "Invoke-Expression")
| table _time host clientip uri_path uri_query status
```

### **Key Indicators**

- `cmd.exe` or `powershell.exe` in URI
- CGI script abuse (e.g. `hello.bat`)
- Long Base64 strings in query parameters

---

## **Step 2 – Decode Base64 Payloads**

### **Observed Encoded String**

```
VABoAGkAcwAgAGkAcwAgAG4AbwB3ACAATQBpAG4AZQAhACAATQBVAEEASABBAEEASABBAEEA
```

### **Decoded Result**

- Reveals attacker message / PowerShell content
- Confirms **obfuscation technique**

### **Why Base64 Is Used**

- Evades basic detection
- Hides intent from logs
- Common in PowerShell attacks

---

## **Step 3 – Inspect Apache Error Logs**

### **Goal**

Check whether malicious input reached backend execution.

### **Splunk Query**

```spl
index=windows_apache_error ("cmd.exe" OR "powershell" OR "Internal Server Error")
```

### **Key Insight**

- `500 Internal Server Error` after malicious request
- Strong signal that:

  - Input was processed
  - Execution attempt occurred

---

## **Step 4 – Detect Process Execution via Apache**

### **Goal**

Confirm OS-level command execution.

### **Splunk Query**

```spl
index=windows_sysmon ParentImage="*httpd.exe"
```

### **Critical Finding**

- Apache (`httpd.exe`) spawning:

  - `cmd.exe`
  - `powershell.exe`

➡️ **Direct evidence of successful command injection**

---

## **Step 5 – Confirm Attacker Reconnaissance**

### **Goal**

Detect attacker verifying execution context.

### **Splunk Query**

```spl
index=windows_sysmon *cmd.exe* *whoami*
```

### **Why This Matters**

- `whoami` is commonly used:

  - Immediately after exploitation
  - To confirm privileges

---

## **Step 6 – Hunt for Encoded PowerShell Execution**

### **Goal**

Check if obfuscated PowerShell payloads actually ran.

### **Splunk Query**

```spl
index=windows_sysmon Image="*powershell.exe"
(CommandLine="*enc*" OR CommandLine="*-EncodedCommand*" OR CommandLine="*Base64*")
```

### **Interpretation**

- **No results** → payload likely blocked
- **Results present** → decode payload → assess impact

---

## **Attack Chain Summary**

1. Command injection via Apache CGI (`hello.bat`)
2. Base64-encoded PowerShell payload delivered
3. Apache spawns `cmd.exe` / `powershell.exe`
4. Attacker runs `whoami`
5. Attempted obfuscated execution

---

## **Answer the Questions**

### **What is the reconnaissance executable file name?**

<details>
  <summary>Click to reveal the answer</summary>

**whoami.exe**

</details>

---

### **What executable did the attacker attempt to run through the command injection?**

<details>
  <summary>Click to reveal the answer</summary>

**PowerShell.exe**

</details>

---

## ✅ **Key Blue Team Takeaways**

- Long HTTP requests ≠ normal behavior
- Base64 in URLs = **high-risk signal**
- Apache spawning OS shells = **critical compromise**
- Sysmon ParentImage correlation is powerful
- Splunk pivots = full attack reconstruction

---
