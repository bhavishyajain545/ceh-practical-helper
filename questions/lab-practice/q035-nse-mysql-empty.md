# Q035 — MySQL Empty Password NSE

| Field | Value |
|---|---|
| **Target** | `192.168.52.129` (Metasploitable 2) |
| **Domain** | 03 — Enumeration |
| **Difficulty** | 🟡 Medium |
| **Tools** | `enum4linux`, `smbclient`, `rpcclient`, `nmap NSE` |
| **Time budget** | 10–15 min |

---

## 📝 Question

Use `nmap --script mysql-empty-password -p 3306 192.168.52.129`. Report whether root login is available.

---

## 🎯 Flag Format

```
root_empty=<yes|no>
```

Example: `root_empty=yes`

**⚠️ Format strict hai** — lowercase, no spaces around `=`, semicolon separator where shown.

---

## 💡 Hints

<details>
<summary>Hint 1 (click)</summary>

MySQL on Metasploitable has blank root password.
</details>

<details>
<summary>Hint 2</summary>

Direct test: `mysql -h <ip> -u root`.
</details>

---

## ✅ Solution

<details>
<summary>Show solution</summary>

```bash
nmap --script mysql-empty-password -p 3306 192.168.52.129
```

Reports `root account has empty password`.

**Answer:** `root_empty=yes`

📖 Ref: [playbooks/enumeration-playbook.md](../../playbooks/enumeration-playbook.md)
</details>

---

## 🤖 Claude Setup Prompt (for Claude-on-your-PC)

```
1. Verify VMs running: ping 192.168.52.129 and ping 192.168.52.130 from 192.168.52.128.
2. Required services for this Q already up by default on the relevant target.
3. If something is down, restart it on the target VM.

Report back: "Lab ready for Q035".
```
