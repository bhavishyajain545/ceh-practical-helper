# CEH Exam Nmap Quick Commands (Manual Type Friendly)

**Use case:** Exam mein copy-paste disabled hai. Yeh **chhote commands** hain — easy to type, fast to remember.

---

## Windows vs Linux

**Nmap dono pe chalta hai:**
- **Linux/Parrot:** `nmap`
- **Windows:** `nmap` (CMD/PowerShell mein, agar installed) ya **Zenmap** (GUI)
- iLabs Parrot mein already installed hai

**Same commands work both OS.** Sirf output path different.

---

## 🎯 The Only 5 Commands You Need

### 1. Live Hosts (Discovery)

```
nmap -sn 172.16.32.0/24
```

Replace IP range per subnet. **Output: list of live IPs.**

---

### 2. Quick Port Scan (Fast)

```
nmap -F <IP>
```

Top 100 ports. **30 seconds.**

---

### 3. Full Scan (Service + Version + Scripts)

```
nmap -sS -sV -sC -p- <IP>
```

**Yeh main command hai.** Saari info — ports, versions, OS, default scripts.

---

### 4. Specific Port + Vuln Check

```
nmap -p 445 --script vuln <IP>
```

Replace `445` with target port. **Scripts:** SMB, FTP, SQL vuln auto-detect.

---

### 5. Service-Specific Quick

```
nmap -p 5555 --open 172.16.32.0/24
```

Replace `5555` with port (5555=Android ADB, 2375=Docker, 3389=RDP, 1433=MSSQL).

---

## 🔥 Port Quick Reference (Yaad Rakh)

| Port | Service | Question Type |
|------|---------|---------------|
| 21 | FTP | Anonymous login, vsftpd vuln |
| 22 | SSH | Hydra brute, key crack |
| 23 | Telnet | Banner grab, brute |
| 25 | SMTP | User enum, relay |
| 53 | DNS | Zone transfer |
| 80 | HTTP | Web, gobuster, SQLi |
| 88 | Kerberos | AS-REP, Kerberoasting |
| 110 | POP3 | Brute |
| 135/139/445 | SMB | EternalBlue, smbclient |
| 161 | SNMP | Community string |
| 389 | LDAP | AD enum |
| 443 | HTTPS | Web SSL |
| 1433 | MSSQL | xp_cmdshell |
| 2049 | NFS | no_root_squash |
| 2375 | Docker | Unauth API |
| 3306 | MySQL | Brute, dump |
| 3389 | RDP | Hydra, xfreerdp |
| 5432 | PostgreSQL | Brute |
| 5555 | Android ADB | adb connect |
| 5900 | VNC | Brute |
| 8080 | HTTP-Alt | Web alt |

---

## ⚡ One-Liners Per Question Type

### Find Android Device
```
nmap -p 5555 --open 172.16.32.0/24
```

### Find Docker Exposed
```
nmap -p 2375 --open 172.16.32.0/24
```

### Find SMB Vulnerable (EternalBlue)
```
nmap -p 445 --script smb-vuln-ms17-010 <IP>
```

### Find FTP Anonymous
```
nmap -p 21 --script ftp-anon <IP>
```

### Find HTTP Vulns
```
nmap -p 80 --script http-enum,http-vuln* <IP>
```

### Find MSSQL Empty Password
```
nmap -p 1433 --script ms-sql-empty-password <IP>
```

### DNS Zone Transfer
```
nmap -p 53 --script dns-zone-transfer --script-args dns-zone-transfer.domain=<domain> <IP>
```

### Stealth Scan
```
nmap -sS -f -T2 <IP>
```

### OS Detect
```
nmap -O <IP>
```

---

## 📋 Exam Time Workflow (3 Commands Only)

**Type karne mein 30 seconds:**

```
nmap -sn 172.16.32.0/24
```
↓ (live hosts dikh gaye)

```
nmap -F <each-live-IP>
```
↓ (open ports dikh gaye)

```
nmap -sV -sC -p- <target-IP>
```
↓ (sab info)

**Done. Question solve karo.**

---

## 🎯 Save Output (Optional)

```
nmap -sV <IP> -oN result.txt
```

File mein save → baad mein dekho.

---

## Common Mistakes (Mat Karna)

1. ❌ Har question ke time `-p-` chalana → time waste
   ✅ Pehle `-F` se quick check, phir `-p-` agar zaroori ho

2. ❌ Bina `-sn` ke directly port scan → galat IP scan
   ✅ Pehle live hosts confirm

3. ❌ Background `&` bhul jaana → terminal block
   ✅ `&` lagao long scans ke liye

4. ❌ `sudo` bhul jaana stealth scan mein
   ✅ `sudo nmap -sS` (root chahiye SYN scan ke liye)

5. ❌ Sirf top 1000 ports check karna
   ✅ `-p-` for all 65535 (agar question kahe "all ports")

---

## Quick Decision Tree

```
Question dekho:
│
├─ "Live hosts/network discovery"
│   → nmap -sn <subnet>
│
├─ "OS detect"
│   → nmap -O <IP>
│
├─ "Specific service vuln"
│   → nmap -p <port> --script vuln <IP>
│
├─ "Service version"
│   → nmap -sV <IP>
│
├─ "Stealth/IDS bypass"
│   → nmap -sS -f -T2 <IP>
│
└─ "All open ports"
    → nmap -p- <IP>
```

---

## TL;DR for Exam

**Type 3 commands max per question:**

1. `nmap -sn <subnet>` (only once at start)
2. `nmap -F <IP>` (quick)
3. `nmap -sV -sC -p- <IP>` (detailed)

**That's it. Don't over-engineer.**

---

## Windows-Specific Note

Windows pe nmap:
- Install: nmap.org se download → Zenmap saath aata hai
- CMD pe `nmap` command direct chalti hai
- iLabs Windows machines pe pre-installed hota hai
- **GUI chahiye toh Zenmap open kar** — visual scan, profile select, target IP daal, "Scan" button

iLabs mein:
- Parrot → terminal mein `nmap`
- Windows VM → CMD ya Zenmap
