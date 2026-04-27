# Q284 — Unshadow and Crack Linux Passwords

| Field | Value |
|-------|-------|
| **Target** | local |
| **Domain** | System Hacking / Password Cracking |
| **Difficulty** | 🟡 Medium |
| **Tools** | `john, unshadow` |
| **Time budget** | 8–12 min |

---

## 📝 Question

"Combine /etc/passwd and /etc/shadow using unshadow, then crack with John."

---

## 🎯 Flag Format

```
combined_file=<name>; cracked_count=<number>
```

---

## ✅ Solution

```bash
unshadow passwd.txt shadow.txt > combined.txt
john --wordlist=/usr/share/wordlists/rockyou.txt combined.txt
john --show combined.txt
```

**Answer:** `combined_file=combined.txt; cracked_count=3`

---

## 🤖 Claude Setup Prompt

Provide passwd.txt and shadow.txt from target. Report back: "Lab ready for Q284"
