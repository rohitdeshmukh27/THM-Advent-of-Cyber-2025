# **Phishing – Phishmas Greetings**

# **Task 1 – Introduction**

Phishing attacks are increasing as TBFC’s email protection platform is offline.
Your role: **triage suspicious emails**, identify phishing attempts, and prevent credential theft.

## **Learning Objectives**

- Identify phishing signals
- Understand common phishing techniques
- Distinguish **spam** vs **phishing**
- Analyse impersonation, spoofing, attachments, punycode, and social engineering

---

# **Task 2 – Spotting Phishing Emails**

## **What is Phishing?**

Attackers craft deceptive emails to:

- Steal credentials
- Deliver malware
- Trick users into financial fraud
- Exfiltrate sensitive information

### **Example — Credential Theft Phish**

```
From: mckisdy.tbfc@gmail.com
Subject: URGENT – VPN access required
Body: "Send your VPN password immediately!"
```

Red flags:
✔ Free Gmail domain
✔ Urgency
✔ Requests credentials
✔ Impersonation

---

## **Spam vs Phishing**

### **Spam (Harmless Noise)**

- Marketing
- Annoying promotions
- Unsolicited newsletters

**Example:**

```
"Upgrade your logistics service for SOC-mas!"
(This is just marketing, not malicious.)
```

### **Phishing (Dangerous)**

- Intent to steal data
- Spoofed identity
- Social engineering
- Malicious links/attachments

---

# **Common Phishing Techniques (with Examples)**

---

## **1. Impersonation**

Attacker pretends to be someone important.

### **Example:**

```
From: McSkidy <mckidy.incident@gmail.com>
Subject: URGENT – send me VPN access now
```

⚠ But TBFC uses _@tbfc.company.com_ emails.

---

## **2. Social Engineering**

Manipulates emotions: urgency, fear, trust, curiosity.

### **Example Signals:**

- “URGENT”
- “Immediately”
- “Don’t call me — my phone is broken.”

---

## **3. Typosquatting & Punycode**

Domains look similar but are fake.

### **Typosquatting Example**

```
g00gle.com
micros0ft-support.com
```

### **Punycode Example**

```
xn--tryhackme-7db.com
(treated as: труhackme.com using Cyrillic letters)
```

**Attack Scenario Example:**
Attacker sends:

```
From: it-support@tbƒc.com
(Letter ƒ instead of f)
```

---

## **4. Spoofing**

Email looks legitimate but the headers expose the attacker.

### **Example:**

Displayed “From:”:

```
McSkidy <mcksidy@tbfc.com>
```

Header reveals:

```
Return-Path: zxwsedr@easterbb.com
SPF: fail
DKIM: fail
DMARC: fail
```

---

## **5. Malicious Attachments**

Examples:

- HTML files leading to fake login pages
- HTA scripts
- ZIPs containing malware

### **Example:**

```
Attached: VoiceMessage.html
```

But HTML can run full JavaScript → credential theft.

---

## **6. Legitimate Services Abuse**

Attackers use:

- OneDrive
- Google Drive
- Dropbox
  To host **fake documents** or **credential harvesters**.

### **Example:**

```
TBFC-IT shared "Christmas Laptop Upgrade Agreement"
Link → Fake OneDrive login page
```

---

## **7. Fake Login Pages**

Most common phishing method today.

### **Example:**

Fake Microsoft page:

```
https://microsoftonline.login-verify444.com/
```

---

## **8. Side-Channel Communications**

Attacker tries to move the conversation outside monitoring tools.

### **Example:**

```
"Share your WhatsApp number so I can send details privately."
```

---

# **Task Answers (Collapsible Format)**

### **Classify the 1st email — what’s the flag?**

<details>
  <summary>Click to reveal the answer</summary>

**THM{yougotnumber1-keep-it-going}**

</details>

---

### **Classify the 2nd email — what’s the flag?**

<details>
  <summary>Click to reveal the answer</summary>

**THM{nmumber2-was-not-tha-thard!}**

</details>

---

### **Classify the 3rd email — what’s the flag?**

<details>
  <summary>Click to reveal the answer</summary>

**THM{Impersonation-is-areal-thing-keepIt}**

</details>

---

### **Classify the 4th email — what’s the flag?**

<details>
  <summary>Click to reveal the answer</summary>

**THM{Get-back-SOC-mas!!}**

</details>

---

### **Classify the 5th email — what’s the flag?**

<details>
  <summary>Click to reveal the answer</summary>

**THM{It-was-just-a-sp4m!!}**

</details>

---

### **Classify the 6th email — what’s the flag?**

<details>
  <summary>Click to reveal the answer</summary>

**THM{number6-is-the-last-one!-DX!}**

</details>

---
