# gpg — GNU Privacy Guard (Encryption/Decryption)

> "Symmetric and asymmetric encryption tool. Exam mein file encrypt/decrypt karna ho toh gpg ya openssl use karo."

**Install check:** `gpg --version` (pre-installed on all Linux)

---

## 🎯 Cheat-flow

| You need to... | Command |
|---|---|
| Symmetric encrypt file | `gpg -c file.txt` |
| Decrypt file | `gpg -d file.txt.gpg` |
| Encrypt non-interactive | `gpg --batch --passphrase "pass" -c file.txt` |
| Decrypt non-interactive | `gpg --batch --passphrase "pass" -d file.gpg` |
| Generate key pair | `gpg --gen-key` |
| List keys | `gpg --list-keys` |
| Export public key | `gpg --export -a "name" > pub.key` |
| Import key | `gpg --import pub.key` |
| Encrypt for recipient | `gpg -e -r "recipient" file.txt` |
| Sign a file | `gpg --sign file.txt` |
| Verify signature | `gpg --verify file.txt.gpg` |

---

## 🔑 Flags

| Flag | Meaning |
|---|---|
| `-c` / `--symmetric` | Symmetric encryption (password-based) |
| `-d` / `--decrypt` | Decrypt |
| `-e` / `--encrypt` | Asymmetric encrypt (needs recipient) |
| `-r` | Recipient for asymmetric encryption |
| `-a` / `--armor` | ASCII output (not binary) |
| `--batch` | Non-interactive mode |
| `--passphrase "x"` | Provide passphrase on command line |
| `--yes` | Overwrite without asking |
| `-o file` | Output to specific file |
| `--sign` | Add digital signature |
| `--verify` | Verify a signature |

---

## 📋 Recipes

```bash
# 1. Encrypt file with password (exam standard)
gpg --batch --passphrase "exampass" --symmetric file.txt

# 2. Decrypt
gpg --batch --passphrase "exampass" -d file.txt.gpg

# 3. Decrypt to specific output
gpg --batch --passphrase "pass" -o decrypted.txt -d encrypted.gpg

# 4. Generate RSA key pair
gpg --full-generate-key

# 5. Encrypt for specific person
gpg -e -r "admin@lab.com" secret.txt

# 6. Sign and encrypt
gpg --sign -e -r "admin@lab.com" contract.pdf

# 7. Verify signature
gpg --verify signed_file.gpg
```

---

## 💡 Exam Tips

- `-c` = **symmetric** (password), `-e` = **asymmetric** (key pair)
- Most exam questions use **symmetric** (`-c` and `-d`)
- Use `--batch --passphrase` for scripted/non-interactive use
- Output file is `filename.gpg` by default
- If gpg asks for passphrase interactively, the password is in the question

---

## ⚠️ Gotchas

- `--passphrase` might need `--pinentry-mode loopback` on newer gpg versions
- Binary output by default — use `-a` for ASCII/Base64 output
- Dont confuse gpg symmetric (`-c`) with openssl symmetric (`enc -aes-256-cbc`)

---

## 🔗 Related

- [openssl.md](openssl.md) — alternative encryption tool
- [hashcat.md](hashcat.md) — if you need to crack gpg passphrase
