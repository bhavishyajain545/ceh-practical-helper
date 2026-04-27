# Q185 — Generate RSA Key Pair Using OpenSSL

| Field | Value |
|-------|-------|
| **Target** | local |
| **Domain** | Cryptography |
| **Difficulty** | 🟡 Medium |
| **Tools** | `openssl` |
| **Time budget** | 5–8 min |

---

## 📝 Question

"Generate a 2048-bit RSA key pair using openssl. Extract the public key. Report the key size and the number of lines in the private key file."

---

## 🎯 Flag Format

```
keysize=<bits>; privkey_lines=<number>
```

Example: `keysize=2048; privkey_lines=28`

---

## 💡 Hints

**Hint 1**

`openssl genrsa -out private.pem 2048`

**Hint 2**

`openssl rsa -in private.pem -pubout -out public.pem`

---

## ✅ Solution

```bash
openssl genrsa -out private.pem 2048
openssl rsa -in private.pem -pubout -out public.pem
wc -l private.pem
openssl rsa -in private.pem -text -noout | head -1
```

**Answer:** `keysize=2048; privkey_lines=28`

---

## 🤖 Claude Setup Prompt

No setup needed.

Report back: "Lab ready for Q185 — OpenSSL RSA key generation exercise"
