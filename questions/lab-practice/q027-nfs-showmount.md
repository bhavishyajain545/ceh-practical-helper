# Q027 — NFS Exports

| Field | Value |
|---|---|
| **Target** | `192.168.52.129` (Metasploitable 2) |
| **Domain** | 03 — Enumeration |
| **Difficulty** | 🟢 Easy |
| **Tools** | `nfs-common (showmount)` |
| **Time budget** | 10–15 min |

---

## 📝 Question

Use `showmount -e 192.168.52.129` to list NFS exports. What is the **export path** and its allowed clients?

---

## 🎯 Flag Format

```
export=<path>; clients=<spec>
```

Example: `export=/; clients=*`

**⚠️ Format strict hai** — lowercase, no spaces around `=`, semicolon separator where shown.

---

## 💡 Hints

<details>
<summary>Hint 1 (click)</summary>

`showmount -e <ip>`
</details>

<details>
<summary>Hint 2</summary>

Metasploitable exports `/` to the world.
</details>

---

## ✅ Solution

<details>
<summary>Show solution</summary>

```bash
showmount -e 192.168.52.129
```

`/  *` — root filesystem exported to everyone (huge misconfig).

**Answer:** `export=/; clients=*`

📖 Ref: [playbooks/enumeration-playbook.md](../../playbooks/enumeration-playbook.md)
</details>

---

## 🤖 Claude Setup Prompt (for Claude-on-your-PC)

```
Pre-requisites for Q027:
1. Verify Metasploitable2 (192.168.52.129) up; NFS services running: ssh msfadmin@192.168.52.129 → sudo service portmap status && sudo service nfs-kernel-server status (start if down).
2. Confirm /etc/exports on target contains '/ *' (Metasploitable2 default).
3. From Parrot: rpcinfo -p 192.168.52.129 | grep mountd — must show mountd entry.
4. On Parrot install nfs-common if missing: sudo apt install nfs-common.

Report back: "Lab ready for Q027 — NFS exports visible on 192.168.52.129".
```
