# SNMP PORT OPEN (161 UDP) — Ab Kya Karu?

---

## Pehle Samjho: SNMP Kya Hai?

SNMP = **Simple Network Management Protocol**. Ye network devices (routers, switches, servers, printers) ko
remotely monitor aur manage karne ke liye use hota hai.

**Key Facts:**
- Port: **161 (UDP)** — haan, UDP hai TCP nahi! Isliye normal nmap scan mein nahi dikhta
- SNMP mein **community strings** hote hain — ye basically passwords hain
- `public` = read-only (default) — information padh sakte ho
- `private` = read-write (default) — information change bhi kar sakte ho
- SNMP ek **information goldmine** hai — hostname, users, running processes, installed software, network interfaces — sab kuch nikal sakte ho
- CEH exam mein SNMP = **enumerate karo** type questions

**Real life analogy:** SNMP aise samjho jaise ek building ka security guard jo kisi ko bhi building ka poora blueprint de deta hai — bas ek magic word bolna hota hai (community string = "public").

### OID Kya Hai?
SNMP mein har information ka ek address hota hai jise **OID (Object Identifier)** kehte hain.
Ye ek number chain hoti hai jaise: `1.3.6.1.2.1.1.1.0`

Socho aise: OID = file path of information
- `1.3.6.1.2.1.1.1.0` = system description (OS info)
- `1.3.6.1.4.1.77.1.2.25` = Windows user accounts

Tumhe yaad nahi rakhne — neeche table diya hai!

### SNMP Versions:
| Version | Security          | Exam mein                     |
|---------|-------------------|-------------------------------|
| v1      | Community string (plaintext) | Mostly ye milta hai  |
| v2c     | Community string (plaintext) | Ye bhi common hai    |
| v3      | Username + password + encryption | Rarely poocha jaata |

---

## STEP 1: Confirm SNMP Open Hai

```bash
# IMPORTANT: -sU flag lagao kyunki SNMP UDP pe chalta hai!
nmap -sU -p 161 -sV <IP>
```

**Output mein kya dekho:**
- `161/udp open snmp` — SNMP chal raha hai
- `161/udp open|filtered snmp` — shayad chal raha hai, try karo
- Version info jaise "SNMPv2-SMI" ya "net-snmp"

**WHY `-sU`:** Normal nmap TCP scan karta hai. SNMP UDP pe hai, toh `-sU` zaroori hai warna miss ho jayega!

---

## STEP 2: Community String Dhundo / Brute Force

Pehle default strings try karo, phir brute force:

```bash
# Default community strings manually try karo
snmpwalk -v2c -c public <IP>
snmpwalk -v2c -c private <IP>

# Agar default nahi chale — brute force karo
onesixtyone -c /usr/share/wordlists/seclists/Discovery/SNMP/common-snmp-community-strings.txt <IP>

# Nmap se bhi brute force hota hai
nmap -sU -p 161 --script snmp-brute <IP>

# Metasploit se
msfconsole -q
use auxiliary/scanner/snmp/snmp_login
set RHOSTS <IP>
run
```

**`snmpwalk` flags explained:**
- `-v2c` = SNMP version 2c use karo (mostly kaam karta hai, v1 bhi try kar sakte ho `-v1`)
- `-c public` = community string "public" use karo
- `<IP>` = target ka IP

**Output mein kya dekho:**
- Agar community string sahi hai → bahut saara data screen pe aayega (OIDs + values)
- `Timeout: No Response` → galat community string ya SNMP nahi chal raha
- onesixtyone output: `<IP> [public] Linux server1` → community string "public" kaam kar gaya

---

## STEP 3: System Information Nikalo

Community string mil gaya? Ab information extract karo:

```bash
# Poora system info (bahut saara output aayega)
snmpwalk -v2c -c public <IP> 1.3.6.1.2.1.1

# Specific info nikalo:
snmpwalk -v2c -c public <IP> 1.3.6.1.2.1.1.1.0     # OS / System Description
snmpwalk -v2c -c public <IP> 1.3.6.1.2.1.1.5.0     # Hostname
snmpwalk -v2c -c public <IP> 1.3.6.1.2.1.1.6.0     # System Location
snmpwalk -v2c -c public <IP> 1.3.6.1.2.1.1.4.0     # Admin Contact
snmpwalk -v2c -c public <IP> 1.3.6.1.2.1.1.3.0     # System Uptime
```

**Output example:**
```
SNMPv2-MIB::sysDescr.0 = STRING: Linux metasploitable 2.6.24-16 #1 SMP
```
Iska matlab: Target Linux hai, kernel 2.6.24, hostname "metasploitable"

---

## STEP 4: Users, Processes, Software Enumerate Karo

### Important OID Table (YAAD RAKHO!)

| Kya chahiye                 | OID                              | Command                                              |
|-----------------------------|----------------------------------|------------------------------------------------------|
| System Description (OS)     | 1.3.6.1.2.1.1.1.0              | `snmpwalk -v2c -c public <IP> 1.3.6.1.2.1.1.1.0`  |
| Hostname                    | 1.3.6.1.2.1.1.5.0              | `snmpwalk -v2c -c public <IP> 1.3.6.1.2.1.1.5.0`  |
| Running Processes           | 1.3.6.1.2.1.25.4.2.1.2        | `snmpwalk -v2c -c public <IP> 1.3.6.1.2.1.25.4.2.1.2` |
| Installed Software          | 1.3.6.1.2.1.25.6.3.1.2        | `snmpwalk -v2c -c public <IP> 1.3.6.1.2.1.25.6.3.1.2` |
| User Accounts (Windows)     | 1.3.6.1.4.1.77.1.2.25         | `snmpwalk -v2c -c public <IP> 1.3.6.1.4.1.77.1.2.25`  |
| Network Interfaces          | 1.3.6.1.2.1.2.2.1.2           | `snmpwalk -v2c -c public <IP> 1.3.6.1.2.1.2.2.1.2` |
| Open TCP Ports              | 1.3.6.1.2.1.6.13.1.3          | `snmpwalk -v2c -c public <IP> 1.3.6.1.2.1.6.13.1.3` |
| ARP Table                   | 1.3.6.1.2.1.3.1.1.2           | `snmpwalk -v2c -c public <IP> 1.3.6.1.2.1.3.1.1.2` |
| Routing Table               | 1.3.6.1.2.1.4.21.1            | `snmpwalk -v2c -c public <IP> 1.3.6.1.2.1.4.21.1`  |
| Storage / Disk Info         | 1.3.6.1.2.1.25.2.3.1          | `snmpwalk -v2c -c public <IP> 1.3.6.1.2.1.25.2.3.1` |

**Tip:** Agar poora OID yaad nahi → bas `snmpwalk -v2c -c public <IP>` chalao bina OID ke. SAARA data aa jayega. Phir usme grep karo:

```bash
snmpwalk -v2c -c public <IP> | grep -i "user\|software\|process\|interface"
```

---

## STEP 5: Nmap SNMP Scripts (Quick Enumeration)

Agar OID yaad nahi ya jaldi karna hai:

```bash
# All-in-one scan
nmap -sU -p 161 --script snmp-info,snmp-processes,snmp-interfaces,snmp-netstat <IP>

# Windows specific
nmap -sU -p 161 --script snmp-win32-users <IP>         # Windows users
nmap -sU -p 161 --script snmp-win32-software <IP>      # Installed software
nmap -sU -p 161 --script snmp-win32-services <IP>      # Running services

# Sab SNMP scripts ek saath
nmap -sU -p 161 --script "snmp-*" <IP>
```

**WHY nmap scripts:** OID yaad rakhne ki zaroorat nahi, nmap automatically sahi OID query karta hai aur readable format mein dikhata hai.

---

## STEP 6: snmp-check Tool (Easiest Method)

```bash
snmp-check <IP>
snmp-check -c public <IP>
```

**Ye tool BEST hai beginners ke liye** — ek command mein sab kuch nikal deta hai:
- System info, hostname, users, processes, software, network, storage — sab categorized!

---

## Common Mistakes (Mat Karna Ye)

1. **Normal nmap scan kiya (`nmap <IP>`)** — SNMP UDP pe hai! `-sU` flag lagana ZAROORI hai. Bina iske SNMP port dikhega hi nahi.

2. **`snmpwalk` pe "Timeout" aa raha hai** — Community string galat hai. Default `public` try karo, phir brute force karo onesixtyone se.

3. **v1 aur v2c confuse karna** — Pehle `-v2c` try karo. Nahi chale toh `-v1` try karo. Dono plaintext community strings use karte hain.

4. **Bahut zyada output mein kho jaana** — `snmpwalk` ka output BAHUT lamba hota hai. Pipe through `grep` ya `less`:
   ```bash
   snmpwalk -v2c -c public <IP> | grep -i user
   snmpwalk -v2c -c public <IP> | less
   ```

5. **Windows vs Linux OIDs** — User accounts OID (`1.3.6.1.4.1.77.1.2.25`) sirf WINDOWS pe kaam karta hai. Linux pe ye empty aayega.

6. **UDP scan slow hai** — Haan, `-sU` bahut slow hota hai. Isliye specific port do: `nmap -sU -p 161 <IP>` (poora UDP scan mat karo).

---

## Quick Decision Tree

```
Port 161/UDP Open (SNMP) hai?
|
+-- Community string dhundo
|   |
|   +-- snmpwalk -v2c -c public <IP>
|   |   |
|   |   +-- Data aaya? --> Community string = "public"!
|   |   |                  Aage enumerate karo
|   |   |
|   |   +-- Timeout? --> "private" try karo
|   |       |
|   |       +-- Phir bhi nahi? --> Brute force karo
|   |                              onesixtyone -c wordlist.txt <IP>
|   |
+-- Community string mil gaya? Ab information nikalo:
|   |
|   +-- Quick method: snmp-check <IP>
|   |
|   +-- System info: OID 1.3.6.1.2.1.1.1.0
|   |
|   +-- Users (Windows): OID 1.3.6.1.4.1.77.1.2.25
|   |
|   +-- Processes: OID 1.3.6.1.2.1.25.4.2.1.2
|   |
|   +-- Software: OID 1.3.6.1.2.1.25.6.3.1.2
|   |
|   +-- Sab chahiye? --> snmpwalk -v2c -c public <IP> (bina OID)
|   |                     ya nmap --script "snmp-*"
|   |
+-- Mile hue users ko SSH/FTP/SMB pe brute force karo!
```
