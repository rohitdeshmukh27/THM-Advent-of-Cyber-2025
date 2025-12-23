# **C2 Detection – Command & Carol**

# **Task 1 – Introduction**

## **The Story**

The TBFC is very wary since the last series of attacks by the underlings of King Malhare. They are on full alert for anything happening. But they are getting restless; it is too quiet.

**Sir Elfo of the TBFC** takes the initiative and proposes to hunt for Command and Control traffic using the meticulously collected network traffic. A majority of the TBFC elves object, "**We don't have the time to go through so much network traffic!**"

Sir Elfo chuckles, "Don't fret, for I have a powerful tool to assist us! I present to you **RITA**, Real Intelligence Threat Analytics. We just need to convert our PCAP file to Zeek logs, and RITA will do the rest! Anyone can do it, just follow today's tasks."

---

## **Learning Objectives**

- Convert a PCAP to Zeek logs
- Use RITA to analyze Zeek logs
- Analyze the output of RITA

---

# **Task 2 – Detecting C2 with RITA**

## **The Magic of RITA**

**Real Intelligence Threat Analytics (RITA)** is an open-source framework created by **Active Countermeasures**. Its core functionality is to detect command and control (C2) communication by analyzing network traffic captures and logs.

### **Primary Features**

- **C2 beacon detection**
- **DNS tunneling detection**
- **Long connection detection**
- **Data exfiltration detection**
- **Checking threat intel feeds**
- **Score connections by severity**
- Show the number of hosts communicating with a specific external IP
- Shows the datetime when the external host was first seen on the network

---

## **RITA's Analytics**

The magic behind RITA is its analytics. It correlates several captured fields, including IP addresses, ports, timestamps, and connection durations, among others. Based on the normalized and correlated dataset, RITA runs several analysis modules collecting information like:

- **Periodic connection intervals**
- **Excessive number of DNS queries**
- **Long FQDN**
- **Random subdomains**
- **Volume of data over time** over HTTPS, DNS, or non-standard ports
- **Self-signed or short-lived certificates**
- **Known malicious IPs** by cross-referencing with public threat intel feeds or blocklists

---

## **What is Zeek?**

**RITA only accepts network traffic input as Zeek logs.**

**Zeek** is an open-source network security monitoring (NSM) tool. Zeek is **not** a firewall or IPS/IDS; it does not use signatures or specific rules to take an action. It simply observes network traffic via:

- Configured SPAN ports (used to copy traffic from one port to another for monitoring)
- Physical network taps
- Imported packet captures in the PCAP format

Zeek then analyzes and converts this input into a **structured, enriched output**. This output can be used in:

- Incident detection and response
- Threat hunting

Out of the box, Zeek covers two of the four types of NSM data:

1. **Transaction data** (summarized records of application-layer transactions)
2. **Extracted content data** (files or artifacts extracted, such as executables)

---

## **PCAP, I Convert Ye to Zeek Logs**

Let's get started! A neat feature of Zeek is that it can convert packet captures (PCAPs) into structured logs.

### **Step 1: Navigate to the Home Directory**

Open the VM and start a terminal. Navigate to the home directory:

```bash
ubuntu@tryhackme$ ls
Desktop Documents Downloads Music Pictures Public Templates Videos pcaps zeek_logs
```

- **pcaps directory** — Contains example PCAPs of real-life incidents collected from **Bradly Duncan's blog**. He has a wonderful collection of malware-related PCAPs that cover real-world threats.
- **zeek_logs directory** — Contains Zeek logs created by parsing a PCAP file.

---

### **Step 2: Parse a PCAP File**

Use the following command to parse a PCAP file:

```bash
zeek readpcap <pcapfile> <outputdirectory>
```

**Example:**

```bash
ubuntu@tryhackme$ zeek readpcap pcaps/AsyncRAT.pcap zeek_logs/asyncrat
Starting the Zeek docker container
Zeek logs will be saved to /home/ubuntu/zeek_logs/asyncrat
```

---

### **Step 3: Examine the Generated Logs**

Navigate to the output directory and list its contents:

```bash
ubuntu@tryhackme$ cd /home/ubuntu/zeek_logs/asyncrat/ && ls
capture_loss.log   dns.log      http.log           known_services.log  notice.log        packet_filter.log  software.log  stats.log  x509.log
conn.log           files.log    known_hosts.log    loaded_scripts.log  ocsp.log          reporter.log       ssl.log       weird.log
```

**Zeek Logs Generated:**

We can see the different Zeek logs that were generated. For using RITA, we don't really need to know what is in these logs (although the names are quite self-descriptive); however, if you are interested, you can use `cat` to examine their contents.

**More info:** https://docs.zeek.org/en/master/logs/index.html

---

## **Now, Analyze This RITA**

Now that we have prepared the Zeek logs, we can import them into RITA and unleash its analytics.

### **Step 1: Import Zeek Logs into RITA**

Enter the command below to import the Zeek logs and let RITA do its work:

```bash
ubuntu@tryhackme$ rita import --logs ~/zeek_logs/asyncrat/ --database asyncrat
[REDACTED]
2025-10-23T10:56:58Z INF Initiating new import... dataset=asyncrat directory=/tmp/zeek_logs rebuild=false rolling=false started_at="2025-10-23 10:56:58.079568235 +0000 UTC m=+0.013974881"
2025-10-23T10:56:58Z INF [THREAT INTEL] Updating online feed... feed_url=https://feodotracker.abuse.ch/downloads/ipblocklist.txt
[-] Parsing: /tmp/zeek_logs/conn.log
[-] Parsing: /tmp/zeek_logs/http.log
[-] Parsing: /tmp/zeek_logs/ssl.log
[-] Parsing: /tmp/zeek_logs/dns.log
Log Parsing ⠿ ████████████████████████████████████████ 4 / 4
[REDACTED]
```

Once you enter the command, RITA will parse and analyze the imported logs. A lot of output is produced.

**Note:** It is important to consider the dataset's size. Larger datasets will provide more insights than smaller ones. Smaller datasets are also more prone to false positive entries. The one we are using is rather small, but it contains sufficient data for an initial usable result.

---

### **Step 2: View RITA Results**

View the results by entering:

```bash
rita view <database-name>
```

**Example:**

```bash
ubuntu@tryhackme$ rita view asyncrat
```

After entering the command, we can see a structured terminal window with the results.

---

## **RITA Interface Elements**

The terminal window shows three elements:

1. **Search bar**
2. **Results pane**
3. **Details pane**

---

### **1. Search Bar**

To search, we need to enter a forward slash (`/`). We can then enter our search term and narrow down the results.

**Search utility features:**

- Supports the use of search fields
- Enter `?` while in search mode to see an overview of search fields alongside examples
- Enter `?` again to exit the help page
- Press `Esc` to exit the search functionality

---

### **2. Results Pane**

The results pane includes information for each entry that can quickly help us recognize potential threats.

| Column           | Description                                                                                                                                                               |
| ---------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **Severity**     | A score calculated based on the results of threat modifiers                                                                                                               |
| **Source/Dest**  | Source and destination IP/FQDN                                                                                                                                            |
| **Beacon**       | Beacon likelihood score                                                                                                                                                   |
| **Duration**     | Duration of the connection (long connections can be indicators of compromise; most application layer protocols are stateless and close quickly—exceptions: SSH, RDP, VNC) |
| **Subdomains**   | Connections to subdomains with the same domain name (many subdomains could indicate C2 beacon or data exfiltration)                                                       |
| **Threat Intel** | Lists any matches on threat intel feeds                                                                                                                                   |

**Example findings:**

- An FQDN pointing to `sunshine-bizrate-inc-software[.]trycloudflare[.]com`
- An IP `91[.]134[.]150[.]150`

Move the keyboard arrows to select the first entry. You should then see detailed information in the right pane.

---

### **3. Details Pane**

Apart from the Source and Destination, we have two information categories:

#### **A. Threat Modifiers**

These are criteria to determine the severity and likelihood of a potential threat:

| Threat Modifier                   | Description                                                                                                          |
| --------------------------------- | -------------------------------------------------------------------------------------------------------------------- |
| **MIME type/URI mismatch**        | Flags connections where the MIME type reported in the HTTP header doesn't match the URI                              |
| **Rare signature**                | Points to unusual patterns (e.g., unique user agent string not seen in other connections)                            |
| **Prevalence**                    | Analyzes the number of internal hosts communicating with a specific external host (low percentage can be suspicious) |
| **First Seen**                    | Checks the date an external host was first observed on the network (new hosts are more likely to be threats)         |
| **Missing host header**           | Identifies HTTP connections missing the host header (often an oversight by attackers or misconfigured system)        |
| **Large amount of outgoing data** | Flags connections that send very large amounts of data out from the network                                          |
| **No direct connections**         | Flags connections that don't have any direct connections (can be a sign of complex or hidden C2 communication)       |

---

#### **B. Connection Info**

Connections' metadata and basic connection info:

| Field                                | Description                                                                                               |
| ------------------------------------ | --------------------------------------------------------------------------------------------------------- |
| **Connection count**                 | Number of connections initiated between source and destination (very high number can indicate C2 beacon)  |
| **Total bytes sent**                 | Total amount of bytes sent from source to destination (very high number could indicate data exfiltration) |
| **Port number - Protocol - Service** | Non-standard port numbers warrant investigation; lack of SSL in Service info could also be an indicator   |

---

## **What Is This?**

Now that we have covered the look and feel of RITA, let's focus on the results displayed by RITA. Most of the time, the results that are displayed warrant some attention. Even if the entry does not have a high severity score, it can still be an indicator of compromise.

**Important note:** We are using a smaller dataset, and this has some downsides. For example, the **First Seen** threat modifier is relatively low due to small-timeframe packets being captured. This can affect the Severity score.

In any case, we can still examine the entries and their details and make our own analysis.

---

## **Analyzing the First Entry**

### **Key Observations**

**The first thing we notice:** Long FQDN `sunshine-bizrate-inc-software[.]trycloudflare[.]com`

- A quick search on **VirusTotal** indicates that this URL is flagged as **malicious**
- Wow, that was easy!

**Check the second entry as well:** The destination IP also appears malicious.

**However:** We are lucky to have found known Indicators of Compromise immediately. This is not always the case; attackers often change their infrastructure and rotate IPs and domain names. Therefore, we should look a bit further when we don't have a hit with known IoCs.

---

### **Details Pane Analysis**

When we look at the details pane, we can see more info:

- **RITA included the rare signature threat modifier**
- This indicates that the combination of certain parameters (e.g., SSL certificate details) related to this connection is **uncommon** compared to the rest of the analyzed HTTPS traffic
- Malware or C2 connections often create unique TLS handshake patterns that differ from those of browsers and legitimate clients, making them **stand out** even though the payload is encrypted

---

## **Analyzing the Second Entry**

### **Key Findings**

- ✅ The IP is malicious according to VirusTotal
- ✅ The connection duration is quite long
- ✅ The ports mentioned are **non-standard ports**

**This information warrants further investigation.**

You can pivot on the information you have obtained and dig into the Zeek logs and PCAP file. This is out of scope for this walkthrough, but feel free to dig into it and find out information.

**⚠️ Warning:** Just be cautious, as some of the PCAPs may contain malicious files, domains, and IPs that are still in use.

---

## **Each Will Do His Part**

Now that everyone has gone through the manual, let's hunt those "malrabbits" down! We have put a lot of effort into capturing network traffic.

**Task:** Please analyze the `~/pcaps/rita_challenge.pcap` with RITA and answer the questions below.

**Note:** Use the learned steps to process the PCAP and analyze it with RITA.

---

## **Questions**

### **How many hosts are communicating with malhare.net?**

<details>
  <summary>Click to reveal the answer</summary>

**6**

</details>

### **Which Threat Modifier tells us the number of hosts communicating to a certain destination?**

<details>
  <summary>Click to reveal the answer</summary>

**prevalence**

</details>

### **What is the highest number of connections to rabbithole.malhare.net?**

<details>
  <summary>Click to reveal the answer</summary>

**40**

</details>

### **Which search filter would you use to search for all entries that communicate to rabbithole.malhare.net with a beacon score greater than 70% and sorted by connection duration (descending)?**

<details>
  <summary>Click to reveal the answer</summary>

**dst:rabbithole.malhare.net beacon:>=70 sort:duration-desc**

</details>

### **Which port did the host 10.0.0.13 use to connect to rabbithole.malhare.net?**

<details>
  <summary>Click to reveal the answer</summary>

**80**

</details>

---
