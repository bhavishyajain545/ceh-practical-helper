# 🎯 Lab Practice Questions (150)

> CEH Practical-style questions targeting your **local VMware lab**.
> Mixed difficulty + mixed domains — jaisa actual exam mein aata hai.

## 🖥️ Lab Setup

| Role | VM | IP |
|---|---|---|
| 🗡️ Attacker | Parrot Security 6.3.2 | `192.168.52.128` |
| 🎯 Victim 1 | Metasploitable 2 (Linux) | `192.168.52.129` |
| 🎯 Victim 2 | Windows 7 Enterprise x64 | `192.168.52.130` |

Network: VMware NAT VMnet8 — `192.168.52.0/24`

---

## 📂 Question Structure

Each question is one `.md` file with:

1. **Question** — actual exam-style prompt
2. **Target + Domain + Difficulty + Tools**
3. **Flag format** — kaise submit karna hai (no marks for wrong format!)
4. **Hints** — stuck hone pe
5. **Solution** — full walkthrough (try first without looking!)
6. **🤖 Claude setup prompt** — copy-paste into Claude-on-your-PC to set up the question on the target VM

---

## 🗂 Index

### Scanning & Enumeration (30 Qs)
- [q001-nmap-open-ports.md](q001-nmap-open-ports.md)
- [q002-vsftpd-version.md](q002-vsftpd-version.md)
- … (auto-generated)

### System Hacking (25 Qs)
- …

### Web Apps + SQLi (30 Qs)
- …

### Cred Attacks (15 Qs)
- …

### Crypto + Stego (15 Qs)
- …

### Sniffing + Wireless (10 Qs)
- …

### Malware + Forensics (10 Qs)
- …

### Misc (DoS, Social Eng, Session Hijack) (15 Qs)
- …

---

## 🎮 How to use

1. Open a question md file
2. Copy **Claude setup prompt** → paste into Claude-on-your-PC (with VM access)
3. Claude sets up the target on Metasploitable2/Win7
4. You solve using the main repo (`tools/`, `playbooks/`, `glossary.md`)
5. Submit answer in the flag format
6. Check Solution section after attempting
