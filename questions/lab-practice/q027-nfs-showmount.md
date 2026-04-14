# Q027 — NFS Exports

| Field | Value |
|---|---|
| **Target** | `192.168.52.129` (Metasploitable 2) |
| **Domain** | 03 — Enumeration |
| **Difficulty** | 🟢 Easy |
| **Tools** | `enum4linux`, `smbclient`, `rpcclient`, `nmap NSE` |
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
1. Verify VMs running: ping 192.168.52.129 and ping 192.168.52.130 from 192.168.52.128.
2. Required services for this Q already up by default on the relevant target.
3. If something is down, restart it on the target VM.

Report back: "Lab ready for Q027".
```
