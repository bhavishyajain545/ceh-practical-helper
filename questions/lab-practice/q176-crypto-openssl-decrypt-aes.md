# Q176 — Decrypt AES Encrypted File

| Field | Value |
|-------|-------|
| **Target** | local |
| **Domain** | Cryptography |
| **Difficulty** | 🟡 Medium |
| **Tools** | `openssl` |
| **Time budget** | 5–8 min |

---

## 📝 Question

"An encrypted file `secret.enc` was encrypted using AES-256-CBC with password `decryptme`. Decrypt the file and report the flag inside."

---

## 🎯 Flag Format

```
flag=<string>
```

Example: `flag=DECRYPTED_SUCCESS`

**⚠️ Format strict hai** — lowercase, no spaces around `=`, semicolon separator where shown.

---

## 💡 Hints

**Hint 1**

`openssl enc -d` decrypt karta hai — `-d` flag important hai.

**Hint 2**

`openssl enc -aes-256-cbc -d -pbkdf2 -in secret.enc -pass pass:decryptme`

---

## ✅ Solution

```bash
openssl enc -aes-256-cbc -d -pbkdf2 -in secret.enc -pass pass:decryptme
```

**Answer:** `flag=AES_DECRYPTION_WIN`

---

## 🤖 Claude Setup Prompt

```bash
echo "AES_DECRYPTION_WIN" > /tmp/plain.txt
openssl enc -aes-256-cbc -salt -pbkdf2 -in /tmp/plain.txt -out secret.enc -pass pass:decryptme
```

Report back: "Lab ready for Q176 — secret.enc ready for decryption"
