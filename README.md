# Advent of Cyber 2025 — Personal Notes & Writeups 📄

Welcome to my personal repository of notes, writeups, and clues for the Advent of Cyber 2025 challenges on TryHackMe.  
Use this as your reference while solving the rooms — but beware, I've hidden the fun and surprises.

---

## 🎯 What's Inside

This repository contains comprehensive documentation for the TryHackMe Advent of Cyber 2025 event, including:

- **Day 0**: Advent of Cyber Prep Track — Foundation concepts and setup
- **Day 1**: Linux CLI - Shells Bells — Basic shell commands, file navigation, and system administration
- **Day 2**: Phishing - Merry Clickmas — Social engineering tactics, phishing campaigns with SET (Social-Engineer Toolkit)
- **Day 3**: Splunk Basics - Did you SIEM? — SIEM fundamentals, log analysis, and SPL query mastery
- **Day 4**: AI in Security - old sAInt nick — Leveraging AI for cybersecurity tasks (blue team & red team)
- **Day 5**: IDOR - Santa's Little IDOR — Insecure Direct Object Reference vulnerabilities and web application security
- **Day 6**: Malware Analysis - Egg-xecutable — Static and dynamic malware analysis using sandboxes (PeStudio, Regshot, ProcMon)
- **Day 7**: Network Discovery - Scan-ta Clause — Network scanning with Nmap, service enumeration, and port discovery
- **Day 8**: Prompt Injection - Sched-yule conflict — Exploiting AI systems through prompt injection and chain-of-thought reasoning
- **Day 9**: Passwords - A Cracking Christmas — Password cracking techniques for encrypted files (PDFs and ZIPs) using dictionary and mask attacks
- **Day 10**: SOC Alert Triaging - Tinsel Triage — Alert prioritization, investigation workflow, and using Microsoft Sentinel for security operations
- **Day 11**: XSS - Merry XSSMas — Cross-Site Scripting vulnerabilities, reflected and stored XSS exploitation and prevention
- **Day 12**: Phishing - Phishmas Greetings — Advanced phishing detection, impersonation, typosquatting, punycode attacks, and email analysis
- **Day 13**: YARA Rules - YARA mean one! — YARA rule creation for pattern-based malware detection and threat hunting
- **Day 14**: Containers - DoorDasher's Demise — Docker container security, container escape techniques, and privilege escalation via Docker sockets
- **Day 15**: Web Attack Forensics - Drone Alone — Web attack detection, Apache log analysis, Base64 payload decoding, and correlating web logs with Sysmon
- **Day 16**: Forensics - Registry Furensics — Windows Registry forensics, registry hive analysis, and artifact examination
- **Day 17**: CyberChef - Hoperation Save McSkidy — Data encoding/decoding, CyberChef operations, multi-stage decoding (Base64, XOR, MD5, ROT13, ROT47, Hex)
- **Day 18**: Obfuscation - The Egg Shell File — Code obfuscation techniques, ROT1/ROT13 ciphers, XOR encryption, Base64 encoding, layered obfuscation, and deobfuscation with CyberChef

Each day includes detailed notes, commands, methodology, and practical examples.

---

## 📚 What I've Learned So Far

- **Linux CLI Proficiency**: Essential commands for navigation, file operations, and system monitoring
- **Social Engineering & Phishing**: Understanding psychological triggers, crafting phishing campaigns, and creating fake login pages
- **SIEM & Log Analysis**: Centralized log aggregation, SPL queries, anomaly detection, and full attack lifecycle tracing (recon → exploitation → C2)
- **Threat Detection**: Identifying malicious patterns (unusual user-agents, path traversal, webshell execution, outbound C2 traffic)
- **AI-Assisted Security**: Using AI for faster detection, investigation support, OSINT, and attack surface mapping
- **Web Application Security**: IDOR vulnerabilities and authorization flaws
- **Malware Analysis**: Static vs dynamic analysis, sandbox usage, and using tools like PeStudio, Regshot, and ProcMon
- **Network Discovery**: Identifying open ports, hidden services, and banners using Nmap; interacting with services via FTP, Netcat, DNS, and enumerating host-level ports
- **Prompt Injection**: Exploiting agentic AI by leaking CoT reasoning, listing internal tools, extracting hidden tokens, and executing restricted functions
- **Password Cracking**: Dictionary and mask-based attacks on encrypted files (PDFs, ZIPs) using pdfcrack, fcrackzip, and john the ripper
- **SOC Operations**: Alert triage methodology, prioritization based on severity/impact, incident investigation workflow, and MITRE ATT&CK mapping
- **Cross-Site Scripting (XSS)**: Understanding reflected vs stored XSS, exploitation techniques, cookie theft, and XSS prevention (input sanitization, HttpOnly flags)
- **Advanced Phishing Detection**: Email header analysis, impersonation detection, typosquatting, punycode abuse, and distinguishing spam from malicious phishing
- **YARA Rules**: Pattern-based detection using YARA, creating rules with text/hex/regex strings, threat hunting across files and memory
- **Container Security**: Docker architecture, container vs VM differences, Docker sockets, container escape techniques, and privilege escalation through misconfigured containers
- **Web Attack Forensics**: Detecting command injection in Apache logs, Base64 payload decoding, correlating web and host telemetry using Splunk, and reconstructing attack chains
- **Registry Forensics**: Windows Registry structure, forensic artifact analysis, and investigating system and user activity through registry hives
- **Data Encoding/Decoding**: Encoding vs encryption differences, CyberChef operations, chaining recipes, multi-stage decoding (Base64, XOR with keys, MD5 hash cracking, ROT13/ROT47, hexadecimal), and HTTP header analysis
- **Code Obfuscation**: Understanding obfuscation vs encoding vs encryption, identifying obfuscation patterns (ROT1/ROT13, Base64, XOR), detecting visual clues in obfuscated code, reversing layered obfuscation (multi-stage decoding), and using CyberChef Magic operation for automatic deobfuscation

---

## 📊 Progress Tracker

| Day | Challenge                              | Status      |
| --- | -------------------------------------- | ----------- |
| 0   | Advent of Cyber Prep Track             | ✅ Complete |
| 1   | Linux CLI - Shells Bells               | ✅ Complete |
| 2   | Phishing - Merry Clickmas              | ✅ Complete |
| 3   | Splunk Basics - Did you SIEM?          | ✅ Complete |
| 4   | AI in Security - old sAInt nick        | ✅ Complete |
| 5   | IDOR - Santa's Little IDOR             | ✅ Complete |
| 6   | Malware Analysis - Egg-xecutable       | ✅ Complete |
| 7   | Network Discovery - Scan-ta Clause     | ✅ Complete |
| 8   | Prompt Injection - Sched-yule conflict | ✅ Complete |
| 9   | Passwords - A Cracking Christmas       | ✅ Complete |
| 10  | SOC Alert Triaging - Tinsel Triage     | ✅ Complete |
| 11  | XSS - Merry XSSMas                     | ✅ Complete |
| 12  | Phishing - Phishmas Greetings          | ✅ Complete |
| 13  | YARA Rules - YARA mean one!            | ✅ Complete |
| 14  | Containers - DoorDasher's Demise       | ✅ Complete |
| 15  | Web Attack Forensics - Drone Alone     | ✅ Complete |
| 16  | Forensics - Registry Furensics         | ✅ Complete |
| 17  | CyberChef - Hoperation Save McSkidy    | ✅ Complete |
| 18  | Obfuscation - The Egg Shell File       | ✅ Complete |

---

## 🤝 Connect With Me

If you have suggestions, feedback, or just want to connect about cybersecurity:

**LinkedIn**: [rohitdeshmukh27](https://www.linkedin.com/in/rohitdeshmukh27/)

Feel free to reach out if you spot any errors or have ideas for improvement!

---

Happy hunting & Merry SOC-mas! 🎅🛡️

_Last Updated: December 20, 2025 | Days 0-18 Complete_
