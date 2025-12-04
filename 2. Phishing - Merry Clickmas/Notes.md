# 🎄 TryHackMe – Merry Clickmas: Phishing

# Task 1: Introduction

In this task, **you will join the TBFC local red team** alongside the elves:

- Recon McRed
- Exploit McRed
- Pivot McRed

You will help them plan and execute their phishing campaign. It is time to determine whether more cyber security awareness training is required.

---

## 🎯 Learning Objectives

- Understand what **social engineering** is
- Learn the **types of phishing**
- Explore **how red teams create fake login pages**
- Use the **Social-Engineer Toolkit (SET)** to send a phishing email

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

# **Task 2 — Phishing Exercise for TBFC**

## **1. Social Engineering**

- Manipulation technique targeting human psychology.
- Goal: Make people perform actions such as:
  - Sharing passwords
  - Opening malicious attachments
  - Approving payments
- Psychological triggers:
  - **Urgency**
  - **Curiosity**
  - **Authority**

> Social engineering = “Human hacking”.

---

## **2. Phishing**

- Subset of social engineering using messages/communication.
- Channels:
  - Email
  - SMS → Smishing
  - Voice calls → Vishing
  - QR codes → Quishing
  - Social media DMs

### **Purpose**

Convince victims to:

- Click a malicious link
- Download/open an attachment
- Submit credentials
- Send money

---

## **🔍 TBFC Anti-Phishing Mnemonics (S.T.O.P)**

### **S.T.O.P #1 (All Things Secured)**

Ask yourself:

1. **S**uspicious?
2. **T**elling me to click something?
3. **O**ffering an amazing deal?
4. **P**ushing me to act now?

### **S.T.O.P #2 (TBFC Awareness Training)**

1. **Slow down** — avoid acting on adrenaline.
2. **Type the address** — don’t click links.
3. **Open nothing unexpected** — verify.
4. **Prove the sender** — check real email/number.

---

## **3. Building the Trap (Fake Login Page)**

- The phishing campaign aims to steal login credentials.
- Strategy:
  - Host a **fake TBFC login portal**.
  - Capture credentials via a Python script.

**Script location on AttackBox:**  
`~/Rooms/AoC2025/Day02`
`Or download it from the button if you are using your own machine.`

### **Run the phishing server**

```bash
cd ~/Rooms/AoC2025/Day02
./server.py
```

### **Output**

```
Starting server on http://0.0.0.0:8000
```

- **0.0.0.0** → listening on all interfaces
- Victim will access:

  - `http://CONNECTION_IP:8000`
  - or `http://127.0.0.1:8000`

---

## **4. Delivery Using Social-Engineer Toolkit (SET)**

### **Launch SET**

```bash
setoolkit
```

### **Navigation Path**

Here it is in **Markdown format**, exactly as you requested:

Social-Engineering Attacks  
 └── Mass Mailer Attack  
 └── E-Mail Attack (Single Email Address)  
 └── One-Time Use Email Template

---

## **📩 Fill in Email Details**

**Subject:**
`Shipping Schedule Changes`

**Send the message as html or plain? 'h' or 'p' [p]:**
`p`

**Body of email (END to finish):**

```
Dear elves,
Kindly note that there have been significant changes to the shipping schedules due to increased shipping orders.
Please confirm the new schedule by visiting http://CONNECTION_IP:8000
Best regards,
Flying Deer
END
```

**Send email to:**
`factory@wareville.thm`

**Use your own server/open relay:**
Choose → `2`

**From address:**
`updates@flyingdeer.thm`

**From name:**
`Flying Deer`

**Username for open-relay**
`Blank`

**Password for open-relay**
`Blank`

**SMTP server:**
`MACHINE_IP`

**Port:**
`25`

**High priority?**
`no`

**Attach file?**
`n`

**Do you want to attach an inline file - [y/n]:**
`n`

SET then sends the phishing email.

> Unless your email is the same or looks legitimate, no reply will be visible.

---

## **5. Capturing Credentials**

- Monitor the terminal running `server.py`.
- When the victim enters credentials, they appear in real-time.

---

## **6. Collected Results**

The red team successfully receives **working credentials**, proving TBFC employees fell for the phishing attack.

---

## **🔐 Answers**

### **1. Password captured from phishing page**

This code will be sent to you in the server.py terminal if the email is sent correctly.

<details>
  <summary>Click to reveal the answer</summary>

**The answer:**  
`unranked-wisdom-anthem`

</details>

### **2. Number of toys expected for delivery (from TBFC mailbox)**

The username is factory and the password is the answer to the question above. After logging in, check the last email from the company.....you will see the number there.

<details>
  <summary>Click to reveal the answer</summary>

**The answer:**  
`1984000`

</details>

---

## **Task Completed**
