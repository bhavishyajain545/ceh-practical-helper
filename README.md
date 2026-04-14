# CEH Practical Helper

> In-exam reference + 3-week study guide for the EC-Council CEH Practical exam.
> Built for **Parrot OS (iLabs Cyber Range)** attacker box with **Windows targets**.

**How to use during the exam:**
1. Open this repo in Firefox (already allowed in iLabs).
2. Read the question → match it to a domain or playbook → copy commands → fill in target IP.
3. If stuck, check `/playbooks/` for decision trees.

---

## 🧭 Navigation — kahan se kya milega

| Folder / file | Purpose | Kab kholo |
|---|---|---|
| **[glossary.md](glossary.md)** | 200+ terms in Hinglish (SUID, NTLM, Kerberoasting, LFI, JWT, PtH...) | Jab koi term samajh nahi aaye |
| **[strategies/](strategies/README.md)** | Hinglish strategies — mindset, time, format, traps | **Pehle padho**, prep ke time |
| **[online-tools/](online-tools/README.md)** | Browser-based tools — CyberChef, CrackStation, Aperi'Solve | Jab terminal tool fail ho ya quick lookup |
| **[exam-day/](exam-day/first-5-commands.md)** | First 5 commands, time mgmt, Parrot tool locations | Exam start hote hi |
| **[playbooks/](playbooks/master-playbook.md)** | Decision trees: keyword → domain | Jab pata nahi kaha se start karu |
| **[tools/](tools/nmap.md)** | 100 tool docs (every flag, recipe, gotcha) | Jab specific tool ka command chahiye |
| **[questions/by-domain/](questions/README.md)** | 207 drill questions, 18 domains | Daily practice |
| **[questions/scenarios/](questions/scenarios/README.md)** | 30 multi-step cross-domain chains | Real exam-style practice |
| **[questions/full-mock-exams/](questions/full-mock-exams/README.md)** | 3 full mock exams (20 Qs × 6 hours each) | Final week (timed) |
| **[00-exam-strategy/](00-exam-strategy/README.md)** to **[18-cryptography/](18-cryptography/README.md)** | 19 domain folders, each with README + commands + walkthroughs | Domain-deep dive |

---

## ⚡ Quick start (3 paths)

**Mai abhi shuru kar raha hu (Day 1):**
1. [strategies/mindset.md](strategies/mindset.md)
2. [strategies/3-week-plan.md](strategies/3-week-plan.md)
3. [glossary.md](glossary.md) — 5 min skim

**Mai exam ke time hu (Day 22):**
1. [exam-day/first-5-commands.md](exam-day/first-5-commands.md)
2. [playbooks/master-playbook.md](playbooks/master-playbook.md) ← Ctrl+F yahan
3. [exam-day/time-management.md](exam-day/time-management.md)

**Mujhe specific tool/term chahiye:**
- Tool name pata hai → [tools/](tools/nmap.md) section niche dekho
- Term confuse hai → [glossary.md](glossary.md) → Ctrl+F
- Domain naam pata hai → niche [Domains table](#-domains-18) dekho

---

## 📖 Glossary (Hinglish — terminology decoded)

Saare confusing CEH/pentest terms ek file mein, simple Hinglish mein. SUID, NTLM, Kerberoasting, LFI, JWT, PtH — sab Ctrl+F friendly.

→ **[glossary.md](glossary.md)** — 200+ terms, 16 categories

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

## 🛠 Tools (dictionary) — **100 tools, all complete**

Every tool has its own page: install/check, syntax, every flag you'll need, common patterns, gotchas. Question solutions link directly into these pages.

### 🔍 Recon & Scanning
[nmap](tools/nmap.md) · [masscan](tools/masscan.md) · [whois](tools/whois.md) · [dig](tools/dig.md) · [nslookup](tools/nslookup.md) · [host](tools/host.md) · [dnsrecon](tools/dnsrecon.md) · [theharvester](tools/theharvester.md) · [sublist3r](tools/sublist3r.md) · [recon-ng](tools/recon-ng.md) · [shodan](tools/shodan.md) · [google-dorks](tools/google-dorks.md) · [hping3](tools/hping3.md)

### 🧮 Enumeration
[enum4linux](tools/enum4linux.md) · [smbclient](tools/smbclient.md) · [smbmap](tools/smbmap.md) · [rpcclient](tools/rpcclient.md) · [nbtscan](tools/nbtscan.md) · [nmblookup](tools/nmblookup.md) · [snmpwalk](tools/snmpwalk.md) · [onesixtyone](tools/onesixtyone.md) · [ldapsearch](tools/ldapsearch.md) · [showmount](tools/showmount.md) · [smtp-user-enum](tools/smtp-user-enum.md) · [finger](tools/finger.md)

### 🎯 Vuln Analysis
[nikto](tools/nikto.md) · [searchsploit](tools/searchsploit.md) · [openvas](tools/openvas.md) · [nuclei](tools/nuclei.md) · [sslscan](tools/sslscan.md) · [wafw00f](tools/wafw00f.md) · [whatweb](tools/whatweb.md) · [wappalyzer](tools/wappalyzer.md)

### 🔐 Cred Attacks (online + offline)
[hydra](tools/hydra.md) · [medusa](tools/medusa.md) · [ncrack](tools/ncrack.md) · [john](tools/john.md) · [hashcat](tools/hashcat.md) · [hashid](tools/hashid.md) · [hash-identifier](tools/hash-identifier.md) · [crackmapexec](tools/crackmapexec.md) · [responder](tools/responder.md) · [mimikatz](tools/mimikatz.md) · [default-creds](tools/default-creds.md)

### 💥 Exploitation
[metasploit](tools/metasploit.md) · [msfvenom](tools/msfvenom.md) · [nc](tools/nc.md) · [impacket](tools/impacket.md) · [evil-winrm](tools/evil-winrm.md) · [linpeas](tools/linpeas.md) · [winpeas](tools/winpeas.md)

### 🌍 Web (recon + exploit)
[burpsuite](tools/burpsuite.md) · [zap](tools/zap.md) · [gobuster](tools/gobuster.md) · [dirb](tools/dirb.md) · [ffuf](tools/ffuf.md) · [wpscan](tools/wpscan.md) · [joomscan](tools/joomscan.md) · [sqlmap](tools/sqlmap.md) · [curl](tools/curl.md) · [xss](tools/xss.md)

### 📡 Sniffing & MITM
[wireshark](tools/wireshark.md) · [tshark](tools/tshark.md) · [tcpdump](tools/tcpdump.md) · [ettercap](tools/ettercap.md) · [bettercap](tools/bettercap.md) · [arpspoof](tools/arpspoof.md) · [dnsspoof](tools/dnsspoof.md)

### 📶 Wireless
[aircrack-ng](tools/aircrack-ng.md) · [airmon-ng](tools/airmon-ng.md) · [airodump-ng](tools/airodump-ng.md) · [aireplay-ng](tools/aireplay-ng.md) · [reaver](tools/reaver.md) · [wash](tools/wash.md) · [wifite](tools/wifite.md)

### 💣 DoS / Social Eng
[hulk](tools/hulk.md) · [slowloris](tools/slowloris.md) · [loic](tools/loic.md) · [setoolkit](tools/setoolkit.md)

### 🦠 Malware Analysis
[file](tools/file.md) · [strings](tools/strings.md) · [md5sum](tools/md5sum.md) · [sha256sum](tools/sha256sum.md) · [peframe](tools/peframe.md) · [peview](tools/peview.md) · [olevba](tools/olevba.md) · [pdfid](tools/pdfid.md) · [yara](tools/yara.md) · [virustotal](tools/virustotal.md) · [sigverif](tools/sigverif.md)

### 🖼 Crypto / Stego / Forensics
[openssl](tools/openssl.md) · [steghide](tools/steghide.md) · [stegcracker](tools/stegcracker.md) · [zsteg](tools/zsteg.md) · [exiftool](tools/exiftool.md) · [binwalk](tools/binwalk.md) · [foremost](tools/foremost.md)

### 📱 Mobile
[apktool](tools/apktool.md) · [jadx](tools/jadx.md) · [dex2jar](tools/dex2jar.md) · [mobsf](tools/mobsf.md) · [adb](tools/adb.md)

### ☁️ Cloud
[aws-cli](tools/aws-cli.md) · [gsutil](tools/gsutil.md) · [cloud_enum](tools/cloud_enum.md) · [s3scanner](tools/s3scanner.md) · [trufflehog](tools/trufflehog.md)

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
