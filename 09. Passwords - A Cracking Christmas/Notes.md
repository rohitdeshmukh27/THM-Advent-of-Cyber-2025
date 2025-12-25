# **Room 9 – Password Cracking (Encrypted Files)**

## **Task 1 – Introduction**

### **The Story**

- Encrypted files labeled **“North Pole Asset List”** were discovered on the servers.
- These contain fragments of Santa’s gift registry.
- Weak passwords protect the PDFs and ZIP archives.
- Goal: **crack the passwords**, reveal contents, and understand how encryption depends entirely on password strength.

### **Learning Objectives**

- Understand how passwords secure PDFs and ZIP files.
- Learn why weak passwords fail despite strong encryption algorithms.
- Perform dictionary + mask attacks.
- Crack an encrypted PDF and ZIP.
- Recognize why strong, complex passwords are necessary.

### **Key Points**

- Password strength determines actual security.
- Older ZIP encryption is weak compared to modern schemes.
- Encryption only protects confidentiality — passwords can still be attacked offline.
- Attackers use:

  - **Dictionary attacks**
  - **Mask / brute-force attacks**
  - **GPU-accelerated cracking**

- Offline attacks avoid lockouts and monitoring, making detection harder.

---

# **Task 2 – Attacks Against Encrypted Files**

## **How Attackers Recover Weak Passwords**

### **Dictionary Attacks**

- Use a **wordlist** (e.g., _rockyou.txt_) of commonly leaked passwords.
- Fast and effective for weak or reused passwords.

### **Mask / Brute-Force Attacks**

- Tries all combinations when length/format is known.
- Mask examples:

  - `?l?l?l?d?d` → 3 letters + 2 digits

- Faster than full brute-force by limiting search space.

---

## **Hands-On Exercise**

### **Step 1 — Identify File Type**

```bash
file flag.pdf
file flag.zip
```

### **Step 2 — Choose the Right Tool**

PDF:

- `pdfcrack`
- `john` via `pdf2john`

ZIP:

- `fcrackzip`
- `john` via `zip2john`

---

## **PDF Dictionary Attack Example**

```bash
pdfcrack -f flag.pdf -w /usr/share/wordlists/rockyou.txt
```

Outputs:

- PDF metadata
- Progress updates
- Recovered **user-password** (plaintext)

---

## **ZIP Dictionary Attack Example**

Convert ZIP to a John-compatible hash:

```bash
zip2john flag.zip > ziphash.txt
```

Crack it:

```bash
john --wordlist=/usr/share/wordlists/rockyou.txt ziphash.txt
```

Show recovered password:

```bash
john --show ziphash.txt
```

---

# **Detection & Telemetry (Defender View)**

### **Indicators of Cracking Tools**

Binaries:

- `john`, `hashcat`, `fcrackzip`, `pdfcrack`, `pdf2john.pl`, `zip2john`, `7z`, `qpdf`

Command-line patterns:

- `--wordlist`
- `--rules`
- `--mask`
- `-a 3`
- References to **rockyou.txt** or **SecLists**

Artifacts:

- Wordlist reads
- `john.pot`, `hashcat.potfile`
- Long-running GPU workloads (`nvidia-smi`)

### **Linux Audit Rules Example**

```bash
auditctl -w /usr/share/wordlists/rockyou.txt -p r -k wordlists_read
auditctl -a always,exit -F exe=/usr/bin/john -k crack_exec
```

---

# **Response Playbook**

- Isolate host
- Capture memory + running processes
- Preserve cracked files + working directories
- Review decrypted documents
- Investigate lateral movement/exfiltration
- Reset affected credentials
- Educate users & tighten password policy

---

# **Questions & Answers**

### **What is the flag inside the encrypted PDF?**

<details><summary>Click to reveal</summary>
THM{Cr4ck1ng_PDFs_1s_34$y}
</details>

---

### **What is the flag inside the encrypted ZIP file?**

<details><summary>Click to reveal</summary>
THM{Cr4ck1n6_z1p$_1s_34$yyyy}
</details>

---
