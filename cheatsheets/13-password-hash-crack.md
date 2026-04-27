# 🔑 PASSWORD HASH MILA — Crack Kaise?

---

## STEP 1: Hash Type Identify Karo

| Length / Format | Type | Hashcat Mode | John Format |
|---|---|---|---|
| 32 hex chars | MD5 | 0 | raw-md5 |
| 40 hex chars | SHA-1 | 100 | raw-sha1 |
| 64 hex chars | SHA-256 | 1400 | raw-sha256 |
| 128 hex chars | SHA-512 | 1700 | raw-sha512 |
| 32 hex (Windows) | NTLM | 1000 | nt |
| `$1$salt$hash` | MD5-crypt | 500 | md5crypt |
| `$5$salt$hash` | SHA-256-crypt | 7400 | sha256crypt |
| `$6$salt$hash` | SHA-512-crypt | 1800 | sha512crypt |
| `$2a$` / `$2b$` | bcrypt | 3200 | bcrypt |
| `*` + 40 hex | MySQL 4.1+ | 300 | mysql-sha1 |

```bash
# Auto-identify:
hashid '<HASH>'
hash-identifier      # interactive
```

---

## STEP 2: Crack Karo

### John the Ripper
```bash
echo "<HASH>" > hash.txt
john --format=<FORMAT> --wordlist=/usr/share/wordlists/rockyou.txt hash.txt
john --show --format=<FORMAT> hash.txt
```

### Hashcat
```bash
echo "<HASH>" > hash.txt
hashcat -m <MODE> hash.txt /usr/share/wordlists/rockyou.txt
hashcat -m <MODE> hash.txt --show
```

---

## CASE: /etc/shadow File Mili
```bash
unshadow passwd.txt shadow.txt > combined.txt
john --wordlist=/usr/share/wordlists/rockyou.txt combined.txt
john --show combined.txt
```

---

## CASE: Windows SAM Dump (hashdump)
```
Format: username:RID:LM_HASH:NTLM_HASH:::
```
```bash
# NTLM part nikalo (4th field)
john --format=nt --wordlist=/usr/share/wordlists/rockyou.txt ntlm_hashes.txt
hashcat -m 1000 ntlm_hashes.txt /usr/share/wordlists/rockyou.txt
```

---

## CASE: Protected File (ZIP/RAR/PDF)
```bash
# Extract hash → crack
zip2john secret.zip > hash.txt && john --wordlist=rockyou.txt hash.txt
rar2john archive.rar > hash.txt && john --wordlist=rockyou.txt hash.txt
pdf2john.pl file.pdf > hash.txt && john --wordlist=rockyou.txt hash.txt
ssh2john.py id_rsa > hash.txt && john --wordlist=rockyou.txt hash.txt
```

---

## CASE: Rockyou Se Nahi Mila
```bash
# Custom wordlist banao
cewl http://<TARGET> -m 6 -w custom.txt
crunch 4 4 0123456789 -o pins.txt

# John with rules
john --wordlist=rockyou.txt --rules hash.txt
```

---

## QUICK DECISION:
```
Hash mila
  ├─ Type identify: hashid / length check
  ├─ Simple hash? → john --format=X / hashcat -m X
  ├─ /etc/shadow? → unshadow → john
  ├─ Windows NTLM? → john --format=nt / hashcat -m 1000
  ├─ ZIP/RAR/PDF? → *2john → extract hash → john
  └─ Rockyou fail? → cewl custom wordlist / john --rules
```
