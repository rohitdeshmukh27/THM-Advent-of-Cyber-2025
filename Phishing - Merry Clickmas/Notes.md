# 🎄 TryHackMe – Merry Clickmas: Phishing

### **Structured Notes (MD Format)**

---

## **Task 1 — Introduction**

### **📝 The Story**

* TBFC (The Best Festival Company) has recently faced multiple cybersecurity threats.
* Local red team begins penetration tests to evaluate employee awareness regarding phishing.
* Authorized phishing exercises help assess whether training is effective.
* You join the red team with:

  * Recon McRed
  * Exploit McRed
  * Pivot McRed
* Objective: Plan and execute a phishing campaign against TBFC employees.

---

### **🎯 Learning Objectives**

* Understand social engineering.
* Learn different types of phishing.
* Explore how red teams build fake login pages.
* Use SET (Social-Engineer Toolkit) for sending phishing emails.

---

### **🖥️ Connecting to the Machine**

* Start **AttackBox**.
* Start **Target Machine** (wait ~2 minutes).
* Use split view if needed.
* Both machines are required:

  * AttackBox → Send phishing email & host phishing page
  * Target VM → Victim environment

---

---

## **Task 2 — Phishing Exercise for TBFC**

## **1. Social Engineering**

* Manipulation technique targeting human psychology.
* Goal: Make people perform actions such as:

  * Sharing passwords
  * Opening malicious attachments
  * Approving payments
* Psychological triggers:

  * **Urgency**
  * **Curiosity**
  * **Authority**

> Social engineering = “Human hacking”.

---

## **2. Phishing**

* Subset of social engineering using messages/communication.
* Channels:

  * Email
  * SMS → Smishing
  * Voice calls → Vishing
  * QR codes → Quishing
  * Social media DMs

### **Purpose**

Convince victims to:

* Click a malicious link
* Download/open an attachment
* Submit credentials
* Send money

---

### **🔍 TBFC Anti-Phishing Mnemonics (S.T.O.P)**

#### **S.T.O.P #1 (All Things Secured)**

Ask yourself:

1. **S**uspicious?
2. **T**elling me to click something?
3. **O**ffering an amazing deal?
4. **P**ushing me to act now?

#### **S.T.O.P #2 (TBFC Awareness Training)**

1. **Slow down** — avoid acting on adrenaline.
2. **Type the address** — don’t click links.
3. **Open nothing unexpected** — verify.
4. **Prove the sender** — check real email/number.

---

## **3. Building the Trap (Fake Login Page)**

* The phishing campaign aims to steal login credentials.
* Strategy:

  * Host a **fake TBFC login portal**.
  * Capture credentials via a Python script.
* Script location on AttackBox:
  `~/Rooms/AoC2025/Day02`

### **Run the phishing server**

```bash
cd ~/Rooms/AoC2025/Day02
./server.py
```

### **Output**

```
Starting server on http://0.0.0.0:8000
```

* **0.0.0.0** → listening on all interfaces
* Victim will access:

  * `http://CONNECTION_IP:8000`
  * or `http://127.0.0.1:8000`

---

## **4. Delivery Using Social-Engineer Toolkit (SET)**

### **Launch SET**

```bash
setoolkit
```

### **Navigation Path**

1. Social-Engineering Attacks
2. → Mass Mailer Attack
3. → E-Mail Attack Single Email Address

---

### **📩 Fill in Email Details**

**Send email to:**
`factory@wareville.thm`

**Use your own server/open relay:**
Choose → `2`

**From address:**
`updates@flyingdeer.thm`

**From name:**
`Flying Deer`

**SMTP server:**
`MACHINE_IP`

**Port:**
`25`

**High priority?**
`no`

**Attach file?**
`n`

**Subject:**
`Shipping Schedule Changes`

**Body of email (END to finish):**

```
Dear elves,
Kindly note that there have been significant changes to the shipping schedules due to increased shipping orders.
Please confirm the new schedule by visiting http://CONNECTION_IP:8000
Best regards,
Flying Deer
END
```

SET then sends the phishing email.

---

## **5. Capturing Credentials**

* Monitor the terminal running `server.py`.
* When the victim enters credentials, they appear in real-time.

---

## **6. Collected Results**

Red team successfully receives **working credentials**, proving TBFC employees fell for the phishing attack.

---

## **🔐 Answers**

### **1. Password captured from phishing page**

**`unranked-wisdom-anthem`**

### **2. Number of toys expected for delivery (from TBFC mailbox)**

**`1984000`**

---

## **Task Completed**

If you enjoyed this room, TryHackMe recommends the **Phishing Prevention** room.

*No further answers required.*

---
