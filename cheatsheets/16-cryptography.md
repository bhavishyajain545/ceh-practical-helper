# 🔐 ENCRYPTED FILE / ENCODED STRING — Decrypt Karo

---

## STEP 1: Encoding Identify Karo

| Looks Like | Type | Decode |
|---|---|---|
| `SGVsbG8=` (ends with =) | Base64 | `echo "SGVsbG8=" \| base64 -d` |
| `48656c6c6f` (hex pairs) | Hex | `echo "48656c6c6f" \| xxd -r -p` |
| `PRU{synt}` (letters shifted) | ROT13 | `echo "PRU{synt}" \| tr 'A-Za-z' 'N-ZA-Mn-za-m'` |
| `%48%65%6c` (percent encoded) | URL encoding | `python3 -c "import urllib.parse; print(urllib.parse.unquote('%48%65%6c'))"` |
| `&#72;&#101;` (numeric) | HTML entities | Browser mein paste karo |
| Binary: `01001000` | Binary | `python3 -c "print(chr(int('01001000',2)))"` |

---

## CASE 1: Base64 Decode
```bash
echo "Q0VIe2ZsYWd9" | base64 -d
base64 -d encoded_file.txt > decoded.txt
```

---

## CASE 2: Hex to ASCII
```bash
echo "4345487b666c61677d" | xxd -r -p
python3 -c "print(bytes.fromhex('4345487b666c61677d').decode())"
```

---

## CASE 3: ROT13 / Caesar Cipher
```bash
echo "PRU{ebg13}" | tr 'A-Za-z' 'N-ZA-Mn-za-m'
# All rotations try karo (brute force):
for i in $(seq 1 25); do echo "Shift $i:"; echo "ENCRYPTED" | tr "$(echo {A..Z} | tr -d ' ')" "$(echo {A..Z} | tr -d ' ' | cut -c$((i+1))-26)$(echo {A..Z} | tr -d ' ' | cut -c1-$i)"; done
```

---

## CASE 4: OpenSSL Decrypt
```bash
# AES decrypt (password pata hai)
openssl enc -aes-256-cbc -d -pbkdf2 -in encrypted.bin -pass pass:PASSWORD

# DES decrypt
openssl enc -des-cbc -d -in encrypted.bin -pass pass:PASSWORD

# Password nahi pata? Question mein hint hoga
```

---

## CASE 5: GPG Decrypt
```bash
gpg --batch --passphrase "PASSWORD" -d file.gpg
gpg -d file.gpg                            # interactive prompt
```

---

## CASE 6: VeraCrypt Volume Mount
```bash
veracrypt --text encrypted.hc /mnt/vc \
  --password=PASSWORD --pim=0 --keyfiles="" --protect-hidden=no
ls /mnt/vc/
cat /mnt/vc/flag.txt
veracrypt -d                               # dismount
```

---

## CASE 7: Multi-Layer Encoding
```bash
# Base64 → Hex → ROT13 chain:
echo "ENCODED" | base64 -d | xxd -r -p | tr 'A-Za-z' 'N-ZA-Mn-za-m'
```
> Ek layer decode karo, output dekho, next layer identify karo

---

## CASE 8: Hash Generate Karo (question asks to hash)
```bash
echo -n "text" | md5sum
echo -n "text" | sha256sum
echo -n "text" | sha1sum
echo -n "text" | openssl dgst -sha512
```
> ⚠️ `echo -n` — `-n` zaroor lagao (no newline)

---

## CASE 9: SSL Certificate Inspect
```bash
echo | openssl s_client -connect <IP>:443 2>/dev/null | openssl x509 -noout -subject -issuer -dates
openssl x509 -in cert.pem -noout -text
```

---

## CASE 10: RSA Key Operations
```bash
# Generate key pair
openssl genrsa -out private.pem 2048
openssl rsa -in private.pem -pubout -out public.pem

# Encrypt with public key
openssl rsautl -encrypt -pubin -inkey public.pem -in plain.txt -out encrypted.bin

# Decrypt with private key
openssl rsautl -decrypt -inkey private.pem -in encrypted.bin
```

---

## QUICK DECISION:
```
Encrypted/encoded data mili
  ├─ Ends with = ? → Base64 decode
  ├─ All hex chars? → xxd -r -p
  ├─ Letters shifted? → ROT13: tr 'A-Za-z' 'N-ZA-Mn-za-m'
  ├─ .enc file? → openssl dec (AES/DES)
  ├─ .gpg file? → gpg -d
  ├─ .hc file? → veracrypt mount
  ├─ Multi-layer? → decode one layer at a time
  ├─ Hash banana hai? → echo -n "text" | md5sum/sha256sum
  └─ SSL cert? → openssl s_client / x509
```
