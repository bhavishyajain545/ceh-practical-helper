# Q181 — Encrypt File Using GPG Symmetric Encryption

| Field | Value |
|-------|-------|
| **Target** | local |
| **Domain** | Cryptography |
| **Difficulty** | 🟢 Easy |
| **Tools** | `gpg` |
| **Time budget** | 5–8 min |

---

## 📝 Question

"Encrypt `confidential.txt` using GPG symmetric encryption with passphrase `gpgpass123`. Report the output file name and encryption method."

---

## 🎯 Flag Format

```
output=<filename>; method=<symmetric|asymmetric>
```

Example: `output=confidential.txt.gpg; method=symmetric`

---

## 💡 Hints

**Hint 1**

`gpg -c confidential.txt` — `-c` symmetric encryption hai.

**Hint 2**

`gpg --batch --passphrase gpgpass123 -c confidential.txt` non-interactive mode.

---

## ✅ Solution

```bash
gpg --batch --passphrase gpgpass123 --symmetric confidential.txt
ls confidential.txt.gpg
```

**Answer:** `output=confidential.txt.gpg; method=symmetric`

---

## 🤖 Claude Setup Prompt

```bash
echo "TOP SECRET: CEH_GPG_ENCRYPTED" > confidential.txt
```

Report back: "Lab ready for Q181 — confidential.txt ready for GPG encryption"
