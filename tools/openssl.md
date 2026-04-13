# openssl — the crypto swiss army knife

> **The cryptography tool.** CEH Practical has ~2–4 crypto Qs: "decrypt this file", "hash this string", "inspect this cert", "generate an RSA keypair". openssl handles all of it.

**Install check (Parrot — already installed):** `openssl version`

---

## 🎯 Cheat-flow: "What crypto op do I need?"

| You need to... | Run this |
|---|---|
| **Encrypt** a file with AES-256 | `openssl enc -aes-256-cbc -salt -in file -out file.enc -k <pass>` |
| **Decrypt** an AES file | `openssl enc -aes-256-cbc -d -in file.enc -out file -k <pass>` |
| Hash a file (SHA-256) | `openssl dgst -sha256 <file>` |
| Hash a string (MD5) | `echo -n "text" \| openssl dgst -md5` |
| Generate RSA private key | `openssl genrsa -out priv.pem 2048` |
| Extract public key from private | `openssl rsa -in priv.pem -pubout -out pub.pem` |
| Sign a file | `openssl dgst -sha256 -sign priv.pem -out file.sig file` |
| Verify a signature | `openssl dgst -sha256 -verify pub.pem -signature file.sig file` |
| Base64 encode | `openssl base64 -in file -out file.b64` |
| Base64 decode | `openssl base64 -d -in file.b64 -out file` |
| **Inspect an SSL cert** on a server | `openssl s_client -connect <host>:443 -showcerts` |
| Read a PEM cert file | `openssl x509 -in cert.pem -text -noout` |
| Check weak ciphers on a host | `openssl s_client -connect host:443 -cipher 'LOW'` |

---

## 🔑 Subcommands you must know cold

| Subcommand | Meaning |
|---|---|
| `enc` | Symmetric encrypt/decrypt |
| `dgst` | Hashing and signing |
| `genrsa` | Generate RSA private key |
| `rsa` | Manipulate RSA keys |
| `genpkey` | Generic key generation |
| `pkey` | Manipulate any key type |
| `x509` | Read/write X.509 certificates |
| `req` | Generate CSRs and self-signed certs |
| `s_client` | TLS client (debugging) |
| `s_server` | TLS server (testing) |
| `base64` | Base64 encode/decode |
| `rand` | Random bytes |
| `ciphers` | List supported ciphers |
| `speed` | Benchmark algorithms |
| `passwd` | Hash a password (crypt/sha256/sha512) |

### `enc` flags
| Flag | Meaning |
|---|---|
| `-aes-256-cbc` | Cipher (also `-aes-128-cbc`, `-des3`, `-aes-256-gcm`) |
| `-in <file>` | Input file |
| `-out <file>` | Output file |
| `-k <pass>` | Password (on cmdline — insecure but fast) |
| `-pass pass:<pass>` | Password (cleaner) |
| `-pass file:pw.txt` | Password from file |
| `-d` | **Decrypt** (default is encrypt) |
| `-a` / `-base64` | Base64-armor output |
| `-salt` | Use salt (default; don't disable) |
| `-pbkdf2` | Use PBKDF2 KDF (modern, recommended) |
| `-iter <n>` | KDF iteration count |
| `-md sha256` | Hash for key derivation |
| `-K <hex>` | Raw key in hex |
| `-iv <hex>` | Raw IV in hex |
| `-p` | Print key/IV used |
| `-nopad` | Disable padding |

### `dgst` flags
| Flag | Meaning |
|---|---|
| `-md5` `-sha1` `-sha256` `-sha512` | Hash algorithm |
| `-sign <key>` | Sign with private key |
| `-verify <key>` | Verify with public key |
| `-signature <file>` | Signature file |
| `-hmac <key>` | HMAC with shared key |

---

## 📋 Command recipes (copy-paste)

### Symmetric encryption — AES-256-CBC with password
```bash
# Encrypt
openssl enc -aes-256-cbc -salt -pbkdf2 -iter 100000 \
  -in secret.txt -out secret.enc -k 'MyPassw0rd'

# Decrypt
openssl enc -aes-256-cbc -d -pbkdf2 -iter 100000 \
  -in secret.enc -out secret.txt -k 'MyPassw0rd'

# Base64-armored (for copy-paste)
openssl enc -aes-256-cbc -a -salt -pbkdf2 \
  -in secret.txt -out secret.b64 -k 'MyPassw0rd'
```

### Hashing
```bash
# File hash
openssl dgst -sha256 file.txt
openssl dgst -md5 file.txt

# String hash (watch the -n to kill newline!)
echo -n "hello" | openssl dgst -sha256
echo -n "hello" | openssl dgst -md5

# HMAC
echo -n "data" | openssl dgst -sha256 -hmac "secret-key"
```

### RSA keygen + sign + verify
```bash
# 1. Generate 2048-bit private key
openssl genrsa -out priv.pem 2048

# 2. Extract public key
openssl rsa -in priv.pem -pubout -out pub.pem

# 3. View private key details
openssl rsa -in priv.pem -text -noout

# 4. Sign a file
openssl dgst -sha256 -sign priv.pem -out msg.sig msg.txt

# 5. Verify the signature
openssl dgst -sha256 -verify pub.pem -signature msg.sig msg.txt
# → "Verified OK"

# 6. Encrypt small data with RSA public key
openssl pkeyutl -encrypt -pubin -inkey pub.pem -in msg.txt -out msg.enc

# 7. Decrypt with private key
openssl pkeyutl -decrypt -inkey priv.pem -in msg.enc -out msg.txt
```

### Base64
```bash
# Encode
openssl base64 -in file.bin -out file.b64
echo -n "hello" | openssl base64

# Decode
openssl base64 -d -in file.b64 -out file.bin
echo "aGVsbG8=" | openssl base64 -d
```

### Inspect a remote SSL certificate
```bash
# Pull cert and show chain
openssl s_client -connect example.com:443 -showcerts </dev/null

# Just the server cert, parsed
openssl s_client -connect example.com:443 </dev/null 2>/dev/null \
  | openssl x509 -text -noout

# Specific SNI host
openssl s_client -connect 10.10.10.5:443 -servername example.com </dev/null

# Check supported ciphers/protocols
openssl s_client -connect host:443 -tls1_2
openssl s_client -connect host:443 -cipher 'RC4'   # test weak ciphers

# Read cert from disk
openssl x509 -in cert.pem -text -noout
openssl x509 -in cert.pem -noout -subject -issuer -dates -fingerprint
```

### Self-signed cert (one-liner)
```bash
openssl req -x509 -newkey rsa:2048 -nodes \
  -keyout key.pem -out cert.pem -days 365 \
  -subj "/CN=test.local"
```

### Password hashing (for /etc/shadow style)
```bash
openssl passwd -6 -salt xyz 'Passw0rd!'   # SHA-512
openssl passwd -1 -salt xyz 'Passw0rd!'   # MD5 crypt
```

### Random bytes / tokens
```bash
openssl rand -hex 16        # 16 random bytes as hex
openssl rand -base64 32     # 32 random bytes b64
```

---

## ⚠️ Gotchas

- **`echo "text"` adds a newline!** Always use `echo -n` when hashing strings, or your hash will differ from the expected answer.
- **`-k` vs `-K`**: lowercase `-k` is a **password** (KDF-derived); uppercase `-K` is a **raw hex key**. Mixing these up is the #1 decryption failure.
- **Default KDF changed:** Old openssl used MD5 KDF; new defaults to SHA-256. If decrypting an old file, try `-md md5`. For brand-new encryption use `-pbkdf2`.
- **Salt matters:** Without `-salt` (or with `-nosalt`), the same pw → same ciphertext. Default is salted.
- **Cipher naming:** `-aes-256-cbc` not `-aes256cbc`. Dashes matter.
- **RSA can only encrypt small data** (< key size). For larger files, encrypt the data with AES and encrypt the AES key with RSA (hybrid).
- **s_client hangs?** Add `</dev/null` to close stdin.
- **"bad decrypt" error** usually = wrong password, wrong cipher, or wrong KDF (`-pbkdf2` / `-md`).
- **Answer format:** If the Q asks for a hash, copy the hex *without* the `SHA256(file)= ` prefix.

---

## 🔗 Related

- [john — crack the hashes you generate](./john.md)
- [Cryptography question bank](../questions/20-cryptography.md)
- [Cryptography playbook](../playbooks/crypto-playbook.md)
