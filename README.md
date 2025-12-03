# Advent of Cyber 2025 — Personal Notes & Writeups 📄

Welcome to my personal repository of notes, writeups, and clues for the Advent of Cyber 2025 challenges on TryHackMe.  
Use this as your reference while solving the rooms — but beware, I’ve hidden the fun and surprises.

---

## 🎯 What’s Inside

- Notes for **Day 1** through **Day 3** (so far) covering:

  - Linux CLI basics
  - Phishing attack simulation
  - Log analysis with Splunk (SIEM)

- Splunk-SPL cheat-sheet & search patterns
- Logical flow of investigations, but _without_ revealing the final flags/answers
- Clues and references to where keys/flags can be found — but no spoilers 😄

---

## 🧑‍💻 How to Use

1. Open a room on TryHackMe as usual (AttackBox / VM / Browser).
2. Use the notes to understand the theory, commands and methodology.
3. When you see a clue (e.g. “Suspicious user-agent”, “malicious IP”, “path traversal attempts”), go back to the room and investigate — the flag might be hidden there.
4. Do **not** rely on this repo for answers. Use it only as a learning-aid and guidance.

---

## ⚠️ Why I Don’t Reveal Flags Here

- Keeps the “fun” of discovery alive.
- Helps you learn and actively think instead of passively copying answers.
- Ensures you don’t spoil the challenge for others (or yourself).

---

## 📚 What I’ve Learned So Far

- Basics of SIEM and why centralized log-analysis matters.
- How to use SPL queries to filter logs, detect anomalies, and trace an attack lifecycle (recon → enumeration → exploitation → exfiltration → C2).
- Recognizing malicious patterns: unusual user-agents (curl/sqlmap/Havij), path traversal attempts, webshell execution, outbound C2 communications.
- Proper methodology: widen view (all logs) → narrow view (suspicious traffic) → correlate across different log sources (web logs + firewall logs).

---

## 🔑 Clues & Key Locations (But No Spoilers)

| What to Look For                                                      | Where to Look                                                                         |
| --------------------------------------------------------------------- | ------------------------------------------------------------------------------------- |
| Malicious IP / Attacker identity                                      | Logs under `web_traffic` filtered by non-browser user-agents                          |
| Suspicious requests (e.g. path traversal, webshell, payload download) | `path` field in web traffic — check for strings like `../`, `.php`, `.bin`, `.zip`    |
| Evidence of SQL injection or automated tools                          | `user_agent` field containing names like `sqlmap`, `Havij`                            |
| Data exfiltration or C2 comms                                         | Cross-reference `web_traffic` with `firewall_logs` (src/dest IPs & bytes transferred) |
| Time of peak activity / attack window                                 | Use `timechart span=1d count` to spot anomalous spikes in event volume                |

---

## ⭐ What’s Next

- I’ll continue adding writeups as new rooms release.
- I plan to build a **flowchart** of typical attack kill-chains (Recon → Exploit → Persistence → Exfiltration → C2) — useful for future real-world log analysis.
- Keep improving: better notes, more context, clearer clues — but still no outright answers.

---

> 🔐 **Pro tip:** Use these notes to guide _your own_ investigation. When you see a “clue”, treat it like a breadcrumb — follow it in the lab, examine logs, and discover the flag yourself.  
> That’s where the real learning (and fun) happens.

---

Happy hunting & Merry SOC-mas! 🎅🛡️
