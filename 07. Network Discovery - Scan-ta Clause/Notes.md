# **Task 1 – Introduction**

## **The Story**

* Objective:

  * Trace HopSec’s actions
  * Regain access to the server
  * Identify hidden services and ports
  * Restore control before corruption completes
* Three malicious “bunnies” are hiding **three Easter-egg keys** on the server.

## **Learning Objectives**

* Understand **network service discovery** fundamentals.
* Learn **core network protocols**: TCP, UDP, DNS, FTP, SSH, HTTP.
* Use **Nmap** for port scanning and analysis.
* Discover exposed services and use them to regain access to the compromised server.

---

# **Task 2 – Discover Network Services**

## **Discovering Exposed Services**

Technical steps for starting the assessment:

1. Identify and target the machine: `tbfc-devqa01` using `MACHINE_IP`.
2. Scan for open ports such as:

   * **22/tcp** — SSH
   * **80/tcp** — HTTP
3. Enumerate discovered services.
4. Exploit exposed services to retrieve **three key parts** needed for admin panel access.

---

### **1. Basic Scan**

```bash
nmap MACHINE_IP
```

**Explanation:**

* Runs the default Nmap scan (top 1000 most common ports).
* Detects port states like **open**, **closed**, **filtered**.
* Useful for quick enumeration.

---

### **2. Full Port Scan with Banner Grabbing**

```bash
nmap -p- --script=banner MACHINE_IP
```

#### Breakdown:

* **`-p-`** → Scan **all 65,535 TCP ports** instead of the default 1000.
* **`--script=banner`** → Uses an NSE script to grab service banners.

  * Helps identify software versions (e.g., OpenSSH, vsFTPd, Apache).

**Why use it?**

* Hidden services often run on non-standard ports.
* Banner grabbing reveals what software is running → helps find vulnerabilities.

---

### **3. UDP Scan**

```bash
nmap -sU MACHINE_IP
```

#### Breakdown:

* **`-sU`** → Enables **UDP scanning**.

**Why is UDP important?**

* Many services run over UDP (DNS, DHCP, SNMP).
* Attackers often hide backdoors on UDP ports because people rarely scan them.

**Note:**

* UDP scans are slower and less reliable because UDP has no handshake.

---

### **4. Combining TCP + UDP Scan (Optional for future use)**

```bash
nmap -sS -sU MACHINE_IP
```

* **`-sS`** → Stealth TCP SYN scan.
* **`-sU`** → UDP scan.

This scans both transport protocols.

---

## **FTP Commands**

### **Accessing FTP on a Non-Standard Port**

```bash
ftp MACHINE_IP 21212
```

* Connects to FTP running on port **21212** instead of default port 21.

### **Anonymous Login**

When prompted for a username:

```
Name: anonymous
```

Many FTP servers allow anonymous access for public files.

### **Listing Files**

```bash
ftp> ls
```

### **Downloading File to Terminal Output**

```bash
ftp> get tbfc_qa_key1 -
```

The **`-`** outputs the file directly to screen instead of saving locally.

---

## **Netcat Commands**

### **Connecting to a Custom Service**

```bash
nc -v MACHINE_IP 25251
```

#### Breakdown:

* **`nc`** → Netcat utility (Swiss army knife for networking).
* **`-v`** → Verbose output (shows connection status).
* **`25251`** → Custom TBFC maintd service.

Use Netcat whenever:

* The service is unknown
* The protocol is not HTTP/FTP/SSH
* You must interact manually with text commands

---

## **DNS Query with DIG**

### **Extracting TXT Records**

```bash
dig @MACHINE_IP TXT key3.tbfc.local +short
```

#### Breakdown:

* **`dig`** → DNS lookup tool.
* **`@MACHINE_IP`** → Query **this specific DNS server**, not your system DNS.
* **`TXT`** → Request a TXT record.
* **`key3.tbfc.local`** → Domain name you are querying.
* **`+short`** → Clean output (only the answer).

TXT records are often used to hide keys or metadata in CTFs.

---

## **Local Service Enumeration**

### **Listing Listening Ports**

```bash
ss -tunlp
```

#### Breakdown:

* **`ss`** → Socket statistics (modern replacement for netstat).
* **`-t`** → Show TCP sockets.
* **`-u`** → Show UDP sockets.
* **`-n`** → Show port numbers instead of service names.
* **`-l`** → Show listening ports.
* **`-p`** → Show process name & PID (if permissions allow).

This reveals:

* Services exposed externally (0.0.0.0)
* Services exposed only locally (127.0.0.1)

---

## **MySQL Enumeration**

### **List Tables**

```bash
mysql -D tbfcqa01 -e "show tables;"
```

#### Breakdown:

* **`-D tbfcqa01`** → Use the `tbfcqa01` database.
* **`-e`** → Execute a single command and exit.

### **Retrieve Flag**

```bash
mysql -D tbfcqa01 -e "select * from flags;"
```

This prints all values stored in the `flags` table.

---

# **Questions & Answers**

### **What evil message appears on the website?**

<details>
  <summary>Click to reveal the answer</summary>

**Pwned by HopSec**

</details>

---

### **What is the first key part found on the FTP server?**

<details>
  <summary>Click to reveal the answer</summary>

**3aster_**

</details>

---

### **What is the second key part found in the TBFC app?**

<details>
  <summary>Click to reveal the answer</summary>

**15_th3_**

</details>

---

### **What is the third key part found in the DNS TXT record?**

<details>
  <summary>Click to reveal the answer</summary>

**n3w_xm45**

</details>

---

### **Which port was the MySQL database running on?**

<details>
  <summary>Click to reveal the answer</summary>

**3306**

</details>

---

### **What is the final flag found in the database?**

<details>
  <summary>Click to reveal the answer</summary>

**THM{4ll_s3rvice5_d1sc0vered}**

</details>
