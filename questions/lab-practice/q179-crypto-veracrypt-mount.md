# Q179 — Mount a VeraCrypt Encrypted Volume

| Field | Value |
|-------|-------|
| **Target** | local |
| **Domain** | Cryptography |
| **Difficulty** | 🟡 Medium |
| **Tools** | `veracrypt` |
| **Time budget** | 8–12 min |

---

## 📝 Question

"A VeraCrypt container `secret_volume.hc` is provided. The password is `veraPass123`. Mount the volume and find the flag file inside."

---

## 🎯 Flag Format

```
flag=<string>
```

Example: `flag=VERACRYPT_MOUNTED`

**⚠️ Format strict hai** — lowercase, no spaces around `=`, semicolon separator where shown.

---

## 💡 Hints

**Hint 1**

CLI: `veracrypt --text secret_volume.hc /mnt/veracrypt --password=veraPass123 --pim=0 --keyfiles="" --protect-hidden=no`

**Hint 2**

Mount hone ke baad `/mnt/veracrypt/` mein files milenge.

---

## ✅ Solution

```bash
veracrypt --text secret_volume.hc /mnt/veracrypt --password=veraPass123 --pim=0 --keyfiles="" --protect-hidden=no
ls /mnt/veracrypt/
cat /mnt/veracrypt/flag.txt
veracrypt -d  # dismount
```

**Answer:** `flag=VERA_CRYPT_SUCCESS`

---

## 🤖 Claude Setup Prompt

1. Create VeraCrypt volume:
```bash
veracrypt --text --create secret_volume.hc --size=5M --password=veraPass123 --encryption=aes --hash=sha-256 --filesystem=fat --pim=0 --keyfiles="" --random-source=/dev/urandom
veracrypt --text secret_volume.hc /mnt/vc --password=veraPass123 --pim=0 --keyfiles="" --protect-hidden=no
echo "VERA_CRYPT_SUCCESS" > /mnt/vc/flag.txt
veracrypt -d
```

Report back: "Lab ready for Q179 — secret_volume.hc VeraCrypt container ready"
