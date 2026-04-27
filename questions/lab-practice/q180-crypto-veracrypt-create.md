# Q180 — Create a VeraCrypt Encrypted Container

| Field | Value |
|-------|-------|
| **Target** | local |
| **Domain** | Cryptography |
| **Difficulty** | 🟡 Medium |
| **Tools** | `veracrypt` |
| **Time budget** | 10–15 min |

---

## 📝 Question

"Create a 10MB VeraCrypt encrypted container named `my_vault.hc` using AES encryption, SHA-256 hash, and password `MyVault2024`. Mount it, create a file `secret.txt` with content `CEH_VAULT_CREATED`, then dismount. Report the encryption algorithm used."

---

## 🎯 Flag Format

```
encryption=<algo>; hash=<algo>; size=<size>
```

Example: `encryption=aes; hash=sha-256; size=10M`

**⚠️ Format strict hai** — lowercase, no spaces around `=`, semicolon separator where shown.

---

## 💡 Hints

**Hint 1**

`veracrypt --text --create` CLI mode mein container banata hai.

**Hint 2**

```
veracrypt --text --create my_vault.hc --size=10M --password=MyVault2024 --encryption=aes --hash=sha-256 --filesystem=fat --pim=0 --keyfiles="" --random-source=/dev/urandom
```

---

## ✅ Solution

```bash
veracrypt --text --create my_vault.hc --size=10M --password=MyVault2024 --encryption=aes --hash=sha-256 --filesystem=fat --pim=0 --keyfiles="" --random-source=/dev/urandom
veracrypt --text my_vault.hc /mnt/vault --password=MyVault2024 --pim=0 --keyfiles="" --protect-hidden=no
echo "CEH_VAULT_CREATED" > /mnt/vault/secret.txt
veracrypt -d
```

**Answer:** `encryption=aes; hash=sha-256; size=10M`

---

## 🤖 Claude Setup Prompt

No special setup needed — student creates the volume themselves.

Report back: "Lab ready for Q180 — VeraCrypt available for container creation"
