> This entire lesson was way too easy.....nothing crazy. 😴😪

# 🎄 AI in Security - old sAInt nick

# Task 1: Introduction

## 🎯 Learning Objectives

- Understand how AI can be used as an assistant in cybersecurity across various roles, domains, and tasks.
- Use an AI assistant to solve different cybersecurity challenges.
- Learn important considerations when using AI in cybersecurity environments.

---

## 🖥️ Connecting to the Machine

You need to **start the AttackBox** and the **target VM**.

### Steps:

1. Start your **target VM** by clicking the **Start Machine** button below.

   - The machine will need about **2 minutes to fully boot**.

2. Start your **AttackBox** by clicking the **Start AttackBox** button.
   - It will open in **split view**.
   - If you cannot see it, click **Show Split View** at the top of the page.
     > Using OpenVPN, you can also use your Kali Linux machine as the attacker computer.

---

# Task 2: AI in Cyber Security — Key Points

### **Why AI is useful**

- Handles large amounts of data quickly.
- Detects anomalies by learning normal behavior.
- Summarizes logs/events (Generative AI).
- Assists defensive security (blue team):

  - Faster detection
  - Investigation support
  - Automated responses

- Supports offensive security (red team):

  - Recon & OSINT
  - Parsing noisy scan results
  - Mapping attack surfaces

- Helps in software security:

  - SAST/DAST scanning
  - Helps identify vulnerabilities

---

## **Important Considerations**

- AI output is **not always accurate** → must be verified.
- AI can overload systems if misused (e.g., during pentests).
- Data privacy and model security must be maintained.
- Users must understand limitations and risks.

---

## **Practical Exercise Overview**

You will interact with **Van SolveIT**, an AI assistant demonstrating:

### **1. Red Team**

- Generate & run an exploit script.

### **2. Blue Team**

- Analyze web logs from an attack.

### **3. Software Security**

- Analyze source code for vulnerabilities.

Access the assistant at:
**[http://<ip.address>](http://<ip.address>)**
(Use AttackBox or your own device with VPN)

---

## **Usage Tips**

- Responses might take a moment to appear.
- Use **Restart Chat** if the AI gets stuck.
- Unlocked stages can be revisited anytime.

---

# **Questions & Answers**

---

### **Question: Complete the AI showcase. What flag is presented to you?**

<details>
  <summary>Click to reveal the answer</summary>

**The answer:**
THM{AI_MANIA}

</details>

---

### **Question: After executing the exploit script, what flag is shown?**

> _Reminder:_ Update the script’s placeholder IP with the actual vulnerable machine:
> **10.48.139.49:5000**

<details>
  <summary>Click to reveal the answer</summary>

**The answer:**
THM{SQLI_EXPLOIT}

</details>

---

### **Question: Additional room recommendation (no answer needed)**

No answer needed.

---
