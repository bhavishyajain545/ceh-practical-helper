# 18 — Cryptography & Steganography

> Expect: a hash to crack, an image to extract data from, or an encrypted file to decrypt. Three mini-flows: **hash → crack**, **image → stego extract**, **cipher → identify → decrypt**.

## 🧭 Decision tree — "I see a crypto/stego question"

```
What are you given?
│
├── Hash string
│   ├── hashid '<HASH>'                    → identify type
│   ├── john --format=<type> hash.txt      → crack with default list
│   ├── john --wordlist=rockyou.txt hash.txt
│   └── hashcat -m <MODE> hash.txt rockyou.txt
│
├── Image file (stego)
│   ├── file image.jpg                      → confirm real type
│   ├── exiftool image.jpg                  → metadata / comments
│   ├── binwalk image.jpg / binwalk -e      → embedded files
│   ├── strings image.jpg | less            → obvious ascii
│   ├── steghide extract -sf image.jpg      → password-protected stego
│   └── stegcracker image.jpg rockyou.txt   → brute force steghide pw
│
├── Encrypted file (.enc, .aes, .gpg)
│   ├── file secret.enc                     → hint at algorithm
│   ├── openssl enc -d -aes-256-cbc -in secret.enc -out plain -k <PASS>
│   ├── gpg --decrypt secret.gpg
│   └── If key unknown → try common passwords / brute
│
├── Cipher text (classical: Caesar / Vigenère / base64)
│   ├── base64 -d <<< '<STR>'
│   ├── echo '<STR>' | rev
│   ├── CyberChef (browser)
│   └── https://www.dcode.fr
│
└── File with unknown type
    ├── file <FILE>
    ├── binwalk <FILE>
    ├── foremost -i <FILE>                  → carve embedded files
    └── strings <FILE>
```

## 📄 Files in this folder

- **[commands.md](commands.md)** — hash / stego / openssl recipes
- **[walkthroughs.md](walkthroughs.md)** — full examples (hash, stego, openssl)
- Question bank: **[../questions/by-domain/18-cryptography.md](../questions/by-domain/18-cryptography.md)**

## 🛠 Tools used in this domain

- **[openssl](../tools/openssl.md)** — encrypt / decrypt / hash
- **[hashid](../tools/hashid.md)** — hash type identifier
- **[hash-identifier](../tools/hash-identifier.md)** — alt identifier
- **[john](../tools/john.md)** — John the Ripper
- **[hashcat](../tools/hashcat.md)** — GPU cracker
- **[steghide](../tools/steghide.md)** — JPEG/BMP/WAV stego
- **[stegcracker](../tools/stegcracker.md)** — steghide brute
- **[exiftool](../tools/exiftool.md)** — metadata reader
- **[binwalk](../tools/binwalk.md)** — binary carver
- **[foremost](../tools/foremost.md)** — file carver
- Online: CyberChef, dcode.fr, crackstation.net

## ✅ Domain checklist

- [ ] Identify MD5 / SHA1 / SHA256 / NTLM / bcrypt at sight
- [ ] Know `hashcat -m` modes: 0=MD5, 100=SHA1, 1400=SHA256, 1000=NTLM, 1800=sha512crypt, 22000=WPA
- [ ] Crack a hash with john + rockyou
- [ ] Run exiftool + binwalk + strings on an image
- [ ] Extract a steghide-protected file with a known password
- [ ] Decrypt an AES file with openssl
- [ ] Base64-decode a blob
- [ ] Done all questions in [the question bank](../questions/by-domain/18-cryptography.md)
