# Q283 — Crack /etc/shadow Using John

| Field | Value |
|-------|-------|
| **Target** | local |
| **Domain** | System Hacking / Password Cracking |
| **Difficulty** | 🟡 Medium |
| **Tools** | `john` |
| **Time budget** | 8–12 min |

---

## 📝 Question

"Crack the passwords in the provided shadow file using John with rockyou.txt."

---

## 🎯 Flag Format

```
user1=<password>; user2=<password>
```

---

## ✅ Solution

```bash
john --wordlist=/usr/share/wordlists/rockyou.txt shadow.txt
john --show shadow.txt
```

**Answer:** `msfadmin=msfadmin; user=user`

---

## 🤖 Claude Setup Prompt

Copy /etc/shadow from Metasploitable2. Report back: "Lab ready for Q283"
