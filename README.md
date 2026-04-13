# CEH Practical Helper

> In-exam reference + 3-week study guide for the EC-Council CEH Practical exam.
> Built for **Parrot OS (iLabs Cyber Range)** attacker box with **Windows targets**.

**How to use during the exam:**
1. Open this repo in Firefox (already allowed in iLabs).
2. Read the question → match it to a domain or playbook → copy commands → fill in target IP.
3. If stuck, check `/playbooks/` for decision trees.

---

## 🧠 Strategies (Hinglish — read these first)

The single most important folder. **Tools sab ke paas hote hain, strategy alag karti hai.**

- **[strategies/mindset.md](strategies/mindset.md)** — sahi soch
- **[strategies/time-strategy.md](strategies/time-strategy.md)** — 6 ghante mein 20 sawal
- **[strategies/question-reading.md](strategies/question-reading.md)** — question kaise padho
- **[strategies/when-stuck.md](strategies/when-stuck.md)** — atak gaye toh
- **[strategies/parallel-work.md](strategies/parallel-work.md)** — ek time pe 3 cheezein
- **[strategies/answer-format.md](strategies/answer-format.md)** — sabse zyada marks yahan
- **[strategies/common-traps.md](strategies/common-traps.md)** — kahan log fail hote hain
- **[strategies/3-week-plan.md](strategies/3-week-plan.md)** — day-by-day plan
- **[strategies/exam-day-checklist.md](strategies/exam-day-checklist.md)** — exam ki subah
- **[strategies/mental-models.md](strategies/mental-models.md)** — sochne ke patterns

→ **[Browse all strategies](strategies/README.md)**

---

## 🌐 Online Tools (browser-based)

Backup arsenal — jab terminal tool fail ho jaye, ya quick lookup chahiye. Sab Firefox se.

- **[online-tools/hash-tools.md](online-tools/hash-tools.md)** — CrackStation, hash lookup
- **[online-tools/crypto-tools.md](online-tools/crypto-tools.md)** — CyberChef + cipher decoders
- **[online-tools/stego-tools.md](online-tools/stego-tools.md)** — Aperi'Solve all-in-one
- **[online-tools/recon-tools.md](online-tools/recon-tools.md)** — DNSDumpster, Shodan, crt.sh
- **[online-tools/web-tools.md](online-tools/web-tools.md)** — JWT.io, vuln DBs
- **[online-tools/exploit-dbs.md](online-tools/exploit-dbs.md)** — Exploit-DB, CVE Details, Rapid7
- **[online-tools/network-tools.md](online-tools/network-tools.md)** — port scanners, traceroute
- **[online-tools/converters.md](online-tools/converters.md)** — base/encoding converters

→ **[Browse all online tools](online-tools/README.md)**

---

## 🚀 Start Here (Exam Day)

- **[First 5 commands you run on every target](exam-day/first-5-commands.md)** ← run these in background while you read the next question
- **[Time management — 20 questions in 6 hours](exam-day/time-management.md)**
- **[Parrot OS tool locations](exam-day/parrot-tool-locations.md)**
- **[Common Windows target ports & default creds](exam-day/windows-targets.md)**

---

## 🧭 Decision Trees (Playbooks)

If you don't know where to start, go here first:

- **[Master playbook — "I just got a question, now what?"](playbooks/master-playbook.md)**
- [Scanning playbook](playbooks/scanning-playbook.md)
- [Hash cracking playbook](playbooks/hash-cracking-playbook.md) *(coming soon)*
- [Web exploit playbook](playbooks/web-playbook.md) *(coming soon)*
- [Crypto / steganography playbook](playbooks/crypto-playbook.md) *(coming soon)*

---

## 📚 Domains (18)

| # | Domain | Status | Top tools |
|---|---|---|---|
| 00 | [Exam strategy](00-exam-strategy/README.md) | 🟡 stub | — |
| 01 | [Recon & Footprinting](01-recon-footprinting/README.md) | 🟡 stub | whois, theHarvester, dnsrecon, sublist3r |
| 02 | **[Scanning](02-scanning/README.md)** | 🟢 **complete (sample)** | nmap, hping3, masscan |
| 03 | [Enumeration](03-enumeration/README.md) | 🟡 stub | enum4linux, smbclient, snmpwalk, ldapsearch |
| 04 | [Vulnerability Analysis](04-vuln-analysis/README.md) | 🟡 stub | nikto, nmap NSE, OpenVAS |
| 05 | [System Hacking](05-system-hacking/README.md) | 🟡 stub | hydra, john, hashcat, mimikatz, metasploit |
| 06 | [Malware Analysis](06-malware-analysis/README.md) | 🟡 stub | strings, PEview, sigverif |
| 07 | [Sniffing](07-sniffing/README.md) | 🟡 stub | wireshark, tcpdump, ettercap |
| 08 | [Social Engineering](08-social-engineering/README.md) | 🟡 stub | SET (setoolkit) |
| 09 | [DoS / DDoS](09-dos/README.md) | 🟡 stub | hping3, hulk |
| 10 | [Session Hijacking](10-session-hijacking/README.md) | 🟡 stub | burp, ettercap, bettercap |
| 11 | [Web Servers](11-web-servers/README.md) | 🟡 stub | nikto, dirb, gobuster |
| 12 | [Web Apps](12-web-apps/README.md) | 🟡 stub | burp, zap, wpscan |
| 13 | [SQL Injection](13-sqli/README.md) | 🟡 stub | sqlmap |
| 14 | [Wireless](14-wireless/README.md) | 🟡 stub | aircrack-ng, airodump-ng |
| 15 | [Mobile](15-mobile/README.md) | 🟡 stub | apktool, jadx |
| 16 | [IoT / OT](16-iot-ot/README.md) | 🟡 stub | shodan, nmap |
| 17 | [Cloud](17-cloud/README.md) | 🟡 stub | aws cli, s3-enum |
| 18 | [Cryptography & Stego](18-cryptography/README.md) | 🟡 stub | openssl, hashcalc, steghide, veracrypt |

---

## 🛠 Tools (dictionary)

Every tool gets its own page with: install/check, syntax, every flag you'll need, common patterns, gotchas. Question solutions link directly into these pages.

- **[nmap](tools/nmap.md)** ← 🟢 complete
- hydra *(coming)*
- john *(coming)*
- hashcat *(coming)*
- sqlmap *(coming)*
- aircrack-ng *(coming)*
- wireshark *(coming)*
- ... 25+ more

---

## ❓ Question Bank

150+ realistic CEH-Practical-style questions, organized by domain. Each question:
- Tells you the **category, difficulty, and tools needed**
- Gives **numbered, copy-paste-ready steps**
- **Links each step** to the relevant tool doc section
- Tells you the **answer format** (so you don't lose marks on formatting)
- Lists **gotchas** (what trips people up)

→ **[Browse all questions](questions/README.md)**

Sample (complete): **[Scanning questions (12)](questions/by-domain/02-scanning.md)**

---

## ⚖️ A note on sources

This repo uses **only legal sources**: HTB Academy, TryHackMe CEH path, public CEH experience blogs, official EC-Council CEH module exercises, and original questions modeled on the public CEH curriculum. **No leaked dumps** — they will get your cert revoked.
