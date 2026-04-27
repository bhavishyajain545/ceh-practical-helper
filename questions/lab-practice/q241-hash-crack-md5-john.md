# Q241 — Crack MD5 Hash Using John

| Field | Value |
|-------|-------|
| **Target** | local |
| **Domain** | Cryptography / Hash Analysis |
| **Difficulty** | 🟡 Medium |
| **Tools** | `john` |
| **Time budget** | 5–10 min |

---

## 📝 Question

"Crack this MD5 hash using John with rockyou.txt: `e10adc3949ba59abbe56e057f20f883e`"

---

## 🎯 Flag Format

```
password=<cracked_password>
```

---

## ✅ Solution

```bash
echo "e10adc3949ba59abbe56e057f20f883e" > hash.txt
john --format=raw-md5 --wordlist=/usr/share/wordlists/rockyou.txt hash.txt
john --show --format=raw-md5 hash.txt
```

**Answer:** `password=123456`

---

## 🤖 Claude Setup Prompt

No setup needed. Report back: "Lab ready for Q241"
