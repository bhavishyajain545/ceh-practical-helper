# Q175 — Encrypt File Using OpenSSL AES-256-CBC

| Field | Value |
|-------|-------|
| **Target** | local |
| **Domain** | Cryptography |
| **Difficulty** | 🟡 Medium |
| **Tools** | `openssl` |
| **Time budget** | 5–8 min |

---

## 📝 Question

"Encrypt the file `plaintext.txt` using AES-256-CBC with openssl. Use password `cehencrypt`. Report the command used and verify by decrypting."

---

## 🎯 Flag Format

```
algorithm=<algo>; encrypted=<yes|no>
```

Example: `algorithm=aes-256-cbc; encrypted=yes`

**⚠️ Format strict hai** — lowercase, no spaces around `=`, semicolon separator where shown.

---

## 💡 Hints

**Hint 1**

`openssl enc -aes-256-cbc -salt -in plaintext.txt -out encrypted.bin -pass pass:cehencrypt`

**Hint 2**

`-pbkdf2` flag add karo for modern openssl versions.

---

## ✅ Solution

```bash
openssl enc -aes-256-cbc -salt -pbkdf2 -in plaintext.txt -out encrypted.bin -pass pass:cehencrypt
# Verify
openssl enc -aes-256-cbc -d -pbkdf2 -in encrypted.bin -pass pass:cehencrypt
```

**Answer:** `algorithm=aes-256-cbc; encrypted=yes`

---

## 🤖 Claude Setup Prompt

```bash
echo "This is a secret message: FLAG_AES_ENCRYPTED" > plaintext.txt
```

Report back: "Lab ready for Q175 — plaintext.txt ready for AES encryption"
