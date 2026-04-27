# Q177 — Crack MD5 Hash Using John the Ripper

| Field | Value |
|-------|-------|
| **Target** | local |
| **Domain** | Cryptography |
| **Difficulty** | 🟡 Medium |
| **Tools** | `john` |
| **Time budget** | 5–10 min |

---

## 📝 Question

"A file `hashes.txt` contains MD5 hashed passwords. Use John the Ripper with `rockyou.txt` to crack them. Report the cracked passwords."

---

## 🎯 Flag Format

```
user1=<password>; user2=<password>
```

Example: `user1=admin123; user2=letmein`

**⚠️ Format strict hai** — lowercase, no spaces around `=`, semicolon separator where shown.

---

## 💡 Hints

**Hint 1**

`john --format=raw-md5 --wordlist=/usr/share/wordlists/rockyou.txt hashes.txt`

**Hint 2**

`john --show hashes.txt` se cracked passwords dikhenge.

---

## ✅ Solution

```bash
john --format=raw-md5 --wordlist=/usr/share/wordlists/rockyou.txt hashes.txt
john --show --format=raw-md5 hashes.txt
```

**Answer:** `user1=password; user2=123456`

---

## 🤖 Claude Setup Prompt

```bash
echo -n "password" | md5sum | awk '{print "admin:"$1}' > hashes.txt
echo -n "123456" | md5sum | awk '{print "user:"$1}' >> hashes.txt
```

Report back: "Lab ready for Q177 — hashes.txt with MD5 hashes ready for cracking"
