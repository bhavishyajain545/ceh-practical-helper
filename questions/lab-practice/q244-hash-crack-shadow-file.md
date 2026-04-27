# Q244 — Crack Linux Shadow File Hashes

| Field | Value |
|-------|-------|
| **Target** | `192.168.52.129` |
| **Domain** | Cryptography / Hash Analysis |
| **Difficulty** | 🔴 Hard |
| **Tools** | `john, unshadow` |
| **Time budget** | 10–15 min |

---

## 📝 Question

"Combine /etc/passwd and /etc/shadow using unshadow, then crack with John and rockyou.txt."

---

## 🎯 Flag Format

```
user1=<password>; user2=<password>
```

---

## ✅ Solution

```bash
unshadow passwd.txt shadow.txt > combined.txt
john --wordlist=/usr/share/wordlists/rockyou.txt combined.txt
john --show combined.txt
```

**Answer:** `msfadmin=msfadmin; user=user`

---

## 🤖 Claude Setup Prompt

Pull /etc/passwd and /etc/shadow from target. Report back: "Lab ready for Q244"
