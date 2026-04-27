# ENCRYPTED/ENCODED DATA — Decrypt Kaise Karu?

---

## Pehle Samjho: Encoding vs Encryption — Bahut Important Difference!

**Encoding** = Data ko ek format se dusre format mein convert karna.
- Koi password/key nahi lagta
- Koi bhi decode kar sakta hai (sirf format pata hona chahiye)
- Purpose: Data transport ke liye (email, URL, etc.)
- Example: Base64, Hex, URL encoding

**Encryption** = Data ko secret key se lock karna.
- Key/password ke bina decode nahi kar sakte
- Purpose: Security / confidentiality
- Example: AES, DES, RSA, GPG

**Hashing** = One-way function — wapas nahi ja sakte.
- Na key hai, na decode hai
- Purpose: Integrity check, password storage
- Example: MD5, SHA-256

```
Encoding:  Hello → SGVsbG8= (Base64) → Hello (koi bhi decode kar sakta hai)
Encryption: Hello + KEY → X#$@! → Hello (sirf KEY wala decode kar sakta hai)
Hashing:   Hello → 185f8db3... (SHA-256) → ??? (wapas nahi ja sakte)
```

> **CEH exam mein:** Pehle identify karo ki encoding hai ya encryption.
> Encoding hai toh seedha decode karo. Encryption hai toh key/password dhundo.

---

## STEP 1: Encoding Type Identify Karo

Encoding ko dekhke pehchano — har type ka apna pattern hota hai:

| Looks Like | Type | Kaise Pehchane | Decode Command |
|---|---|---|---|
| `SGVsbG8=` (ends with `=` ya `==`) | **Base64** | A-Z, a-z, 0-9, +, / chars. End mein `=` padding | `echo "SGVsbG8=" \| base64 -d` |
| `48656c6c6f` (hex pairs, even length) | **Hex** | Sirf 0-9 aur a-f chars. Length hamesha even | `echo "48656c6c6f" \| xxd -r -p` |
| `PRU{synt}` (letters shifted) | **ROT13** | Letters shift hue lagte hain, structure same hai | `echo "PRU{synt}" \| tr 'A-Za-z' 'N-ZA-Mn-za-m'` |
| `%48%65%6c` (percent + hex) | **URL Encoding** | `%XX` pattern har jagah | Python se decode |
| `&#72;&#101;` (ampersand + numbers) | **HTML Entities** | `&#XX;` pattern | Browser mein paste karo |
| `01001000 01100101` (binary) | **Binary** | Sirf 0 aur 1, 8-bit groups | Python se decode |
| `MFZWIZQ=` (A-Z, 2-7) | **Base32** | Sirf uppercase + 2-7 digits | `echo "MFZWIZQ=" \| base32 -d` |

### Quick Identification Tips:
- **`=` ya `==` end mein?** → Almost sure Base64 (ya Base32)
- **Sirf hex chars (0-9, a-f)?** → Hex encoding
- **`%` signs bahut hain?** → URL encoding
- **Letters readable lagte hain but shifted?** → ROT13 ya Caesar
- **Bahut lambi string, mix of everything?** → Base64 (sabse common)

---

## CASE 1: Base64 Decode

Base64 sabse common encoding hai. Email attachments, web tokens, CTFs — har jagah milti hai.

```bash
# String decode karo
echo "Q0VIe2ZsYWd9" | base64 -d
# Output: CEH{flag}

# File decode karo
base64 -d encoded_file.txt > decoded.txt
cat decoded.txt

# Multiple lines ka Base64
cat long_encoded.txt | base64 -d > decoded_output
# Agar file mein multi-line Base64 hai toh cat se pipe karo
```

**Kaise pehchane Base64?**
- Characters: A-Z, a-z, 0-9, +, /
- Length 4 ka multiple hoti hai
- End mein `=` (1 byte padding) ya `==` (2 byte padding) ho sakta hai
- Lekin bina `=` ke bhi Base64 ho sakta hai!

> **Common galti:** Base64 string mein spaces ya newlines → decode fail
> Solution: `echo "SGVs bG8=" | tr -d ' \n' | base64 -d`

---

## CASE 2: Hex to ASCII

Hex encoding mein har character ko 2 hex digits se represent karte hain.

```bash
# xxd se decode karo
echo "4345487b666c61677d" | xxd -r -p
# Output: CEH{flag}
# -r = reverse (hex to binary), -p = plain format

# Python se (agar xxd mein issue aaye)
python3 -c "print(bytes.fromhex('4345487b666c61677d').decode())"
# Output: CEH{flag}

# File mein hex hai toh
xxd -r -p hex_file.txt > decoded.txt
```

**Kaise pehchane Hex?**
- Sirf characters: 0-9, a-f (ya A-F)
- Length hamesha even number hogi (kyunki 2 chars = 1 byte)
- Kabhi kabhi `0x` prefix hota hai: `0x48656c6c6f`
- Space-separated bhi ho sakta hai: `48 65 6c 6c 6f`

---

## CASE 3: ROT13 / Caesar Cipher

ROT13 mein har letter ko 13 positions rotate karte hain. Caesar cipher mein koi bhi shift ho sakta hai.

```bash
# ROT13 decode (shift 13)
echo "PRU{ebg13}" | tr 'A-Za-z' 'N-ZA-Mn-za-m'
# Output: CEH{rot13}
# tr command letters ko 13 positions shift karta hai

# Saari 25 shifts try karo (brute force Caesar)
for i in $(seq 1 25); do
  echo "=== Shift $i ==="
  echo "KHOOR" | python3 -c "
import sys
text = sys.stdin.read().strip()
shift = $i
result = ''
for c in text:
    if c.isalpha():
        base = ord('A') if c.isupper() else ord('a')
        result += chr((ord(c) - base + shift) % 26 + base)
    else:
        result += c
print(result)"
done
# Har shift ka output dekho — kaunsa readable hai wo answer hai
```

**Kaise pehchane?**
- Text readable lagta hai lekin letters galat hain
- Structure same hai (spaces, punctuation same jagah)
- Common pattern: `CEH` → `PRU` (ROT13)

> **Pro tip:** CyberChef (online tool) mein "Magic" recipe use karo —
> ye automatically detect kar leta hai kaunsa encoding/cipher hai.

---

## CASE 4: URL Encoding Decode

URL encoding mein special characters ko `%XX` format mein likha jaata hai.

```bash
# Python se decode karo
python3 -c "import urllib.parse; print(urllib.parse.unquote('%43%45%48%7B%66%6C%61%67%7D'))"
# Output: CEH{flag}

# Bash mein (simple cases)
echo -e '\x43\x45\x48\x7B\x66\x6C\x61\x67\x7D'
# Output: CEH{flag}
```

---

## CASE 5: OpenSSL Decrypt (AES / DES Encrypted File)

Agar `.enc` file di hai ya question mein "AES encrypted" likha hai:

```bash
# AES-256-CBC decrypt (sabse common)
openssl enc -aes-256-cbc -d -pbkdf2 -in encrypted.bin -out decrypted.txt -pass pass:PASSWORD
# -d = decrypt mode
# -pbkdf2 = key derivation (newer OpenSSL)
# -pass pass:PASSWORD = password directly dena
# -in = input encrypted file
# -out = output decrypted file

# AES-128-CBC decrypt
openssl enc -aes-128-cbc -d -in encrypted.bin -out decrypted.txt -k PASSWORD

# DES decrypt
openssl enc -des-cbc -d -in encrypted.bin -out decrypted.txt -pass pass:PASSWORD

# Agar "bad magic number" error aaye:
openssl enc -aes-256-cbc -d -in encrypted.bin -out decrypted.txt -pass pass:PASSWORD -md md5
# -md md5 try karo (older file format)

# Algorithm nahi pata? Common ones try karo:
for algo in aes-256-cbc aes-128-cbc des-cbc des3; do
  echo "=== Trying $algo ==="
  openssl enc -$algo -d -in encrypted.bin -out test.txt -pass pass:PASSWORD 2>/dev/null && echo "SUCCESS: $algo" && break
done
```

**Password kahan se milega?**
- Question text mein directly diya hoga
- Dusri file mein ho sakta hai
- Previous step mein mila hoga (multi-step challenge)

---

## CASE 6: GPG Decrypt

GPG (GNU Privacy Guard) se encrypted files `.gpg` ya `.asc` extension mein hoti hain.

```bash
# Interactive decrypt (password prompt aayega)
gpg -d file.gpg
# Terminal mein decrypted content dikhega

# Non-interactive (password command mein dena)
gpg --batch --passphrase "PASSWORD" -d file.gpg > decrypted.txt
# --batch = no prompt mode
# --passphrase = password directly

# Agar .asc file hai (ASCII armored)
gpg -d message.asc
```

> **GPG ka output:** Decrypt hone pe seedha content terminal mein aata hai.
> File mein save karna ho toh `> output.txt` lagao.

---

## CASE 7: VeraCrypt Volume Mount Karo

VeraCrypt encrypted container ko mount karke files dekh sakte ho.

```bash
# Mount karo
veracrypt --text encrypted.hc /mnt/vc \
  --password=PASSWORD --pim=0 --keyfiles="" --protect-hidden=no
# --text = command line mode (no GUI)
# encrypted.hc = encrypted volume file
# /mnt/vc = mount point (pehle mkdir /mnt/vc karo agar nahi hai)
# --pim=0 = default PIM
# --keyfiles="" = no key file
# --protect-hidden=no = hidden volume protection off

# Mount hone ke baad files dekho
ls /mnt/vc/
cat /mnt/vc/flag.txt

# Kaam ho gaya? Unmount karo
veracrypt -d
# -d = dismount all volumes
```

> **VeraCrypt volume pehchano kaise?** `.hc` extension, ya question mein "VeraCrypt" likha hoga.
> File ka koi magic byte nahi hota (random data jaisi dikhti hai).

---

## CASE 8: Multi-Layer Encoding

Kabhi kabhi data multiple times encode hota hai. Ek ek layer solve karo.

```bash
# Example: Base64 → Hex → ROT13
# Step 1: Base64 decode
echo "NDM0NTQ4N2I2NjZjNjE2Nzdk" | base64 -d
# Output: 434548 7b666c61677d

# Step 2: Hex decode
echo "4345487b666c61677d" | xxd -r -p
# Output: CEH{flag}

# Agar aur bhi layers hain toh continue karo...
```

**Multi-layer approach:**
1. Output dekho — kya lagta hai ye?
2. Base64 lagta hai? Decode karo.
3. Hex lagta hai? `xxd -r -p`
4. Shifted text lagta hai? ROT13 try karo.
5. Tab tak repeat karo jab tak readable output na aaye.

> **Pipeline mein bhi kar sakte ho:**
> `echo "ENCODED" | base64 -d | xxd -r -p | tr 'A-Za-z' 'N-ZA-Mn-za-m'`

---

## CASE 9: Hash Generate Karo (Agar Question Maange)

Kabhi kabhi question mein hash banana hota hai (generate, verify karne ke liye).

```bash
# MD5 hash
echo -n "password123" | md5sum
# Output: 482c811da5d5b4bc6d497ffa98491e38  -

# SHA-1 hash
echo -n "password123" | sha1sum

# SHA-256 hash
echo -n "password123" | sha256sum

# SHA-512 hash
echo -n "password123" | openssl dgst -sha512

# File ka hash nikalo
md5sum file.txt
sha256sum file.txt
```

> **BAHUT IMPORTANT:** `echo -n` mein `-n` zaroor lagao!
> Bina `-n` ke echo ek newline character add karta hai → hash galat aayega!
> `echo "text"` → "text\n" ka hash banega (GALAT)
> `echo -n "text"` → "text" ka hash banega (SAHI)

---

## CASE 10: SSL/TLS Certificate Inspect Karo

```bash
# Live server ka certificate dekho
echo | openssl s_client -connect target.com:443 2>/dev/null | \
  openssl x509 -noout -subject -issuer -dates
# Output:
# subject= CN=target.com, O=Target Inc
# issuer= CN=Let's Encrypt Authority
# notBefore=Jan 1 2024
# notAfter=Apr 1 2024

# Certificate file (.pem / .crt) inspect karo
openssl x509 -in cert.pem -noout -text
# Pura certificate detail — CN, SAN, validity, algorithm sab

# Specific fields
openssl x509 -in cert.pem -noout -subject     # Common Name
openssl x509 -in cert.pem -noout -dates        # Validity dates
openssl x509 -in cert.pem -noout -serial       # Serial number
openssl x509 -in cert.pem -noout -fingerprint  # Fingerprint
```

---

## CASE 11: RSA Key Operations

```bash
# RSA key pair generate karo
openssl genrsa -out private.pem 2048
# 2048-bit private key banega

# Private se public key nikalo
openssl rsa -in private.pem -pubout -out public.pem

# Public key se encrypt karo
openssl rsautl -encrypt -pubin -inkey public.pem -in plain.txt -out encrypted.bin

# Private key se decrypt karo
openssl rsautl -decrypt -inkey private.pem -in encrypted.bin -out decrypted.txt
cat decrypted.txt

# Key details dekho
openssl rsa -in private.pem -text -noout
# Modulus, exponent, prime numbers — sab dikhega
```

---

## Common Mistakes (Mat Karna Ye)

1. **Encoding aur Encryption confuse karna** → Base64 ko "encrypted" samajh ke key dhund rahe ho
   - Base64 encoding hai — seedha decode hota hai, key nahi chahiye!

2. **`echo -n` mein `-n` bhool jaana** → Hash galat aayega
   - Hamesha `echo -n "text"` use karo jab hash banana ho

3. **OpenSSL algorithm galat dena** → "bad decrypt" error
   - Question mein algorithm likha hoga — dhyan se padho
   - Common: aes-256-cbc, aes-128-cbc, des-cbc

4. **Multi-layer mein patience nahi rakhna** → Ek layer decode kiya, aage nahi dekha
   - Har decode ke baad output ko dhyan se dekho — aur layers ho sakti hain

5. **Base64 mein whitespace** → Decode fail
   - `tr -d ' \n'` se spaces/newlines hata ke decode karo

6. **ROT13 ko manually solve karna** → Time waste
   - `tr 'A-Za-z' 'N-ZA-Mn-za-m'` — ek command mein ho jaata hai

7. **VeraCrypt mount point nahi banaya** → Mount fail
   - Pehle `mkdir -p /mnt/vc` karo, phir mount karo

---

## Quick Decision Tree

```
Encrypted/Encoded data mili — kya karu?
  │
  ├─ STEP 1: Kya hai ye? Identify karo
  │   ├─ = ya == end mein? → Base64 → echo "..." | base64 -d
  │   ├─ Sirf 0-9, a-f (even length)? → Hex → xxd -r -p
  │   ├─ Letters shifted lagte hain? → ROT13 → tr command
  │   ├─ %XX pattern? → URL encoding → python3 decode
  │   └─ Samajh nahi aa raha? → CyberChef "Magic" recipe try karo
  │
  ├─ STEP 2: Encrypted file hai?
  │   ├─ .enc file? → openssl enc -aes-256-cbc -d ...
  │   ├─ .gpg / .asc file? → gpg -d file.gpg
  │   ├─ .hc file? → veracrypt mount
  │   └─ Password kahan? → question text / dusri file / previous step
  │
  ├─ STEP 3: Hash banana hai?
  │   └─ echo -n "text" | md5sum / sha256sum (-n bhoolna mat!)
  │
  ├─ STEP 4: Certificate inspect?
  │   └─ openssl x509 -in cert.pem -noout -text
  │
  └─ STEP 5: Multi-layer?
      └─ Ek layer decode karo → output dekho → next layer identify karo → repeat
```
