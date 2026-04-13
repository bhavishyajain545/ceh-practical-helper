# 18 Cryptography & Stego — copy-paste commands

## Hash identification

```bash
# hashid — gives most likely types
hashid '<HASH>'
hashid -m '<HASH>'              # show hashcat modes too

# hash-identifier (interactive)
hash-identifier

# Quick sanity by length
# 32 hex = MD5, 40 hex = SHA1, 64 hex = SHA256, 128 hex = SHA512
echo -n '<HASH>' | wc -c
```

→ See [hashid.md](../tools/hashid.md)

## Cracking with john

```bash
# Auto-detect
john hash.txt

# Force format
john --format=raw-md5 hash.txt
john --format=NT hash.txt
john --format=sha512crypt hash.txt

# With wordlist
john --wordlist=/usr/share/wordlists/rockyou.txt hash.txt

# With rules
john --wordlist=rockyou.txt --rules=Jumbo hash.txt

# Show cracked
john --show hash.txt
```

→ See [john.md](../tools/john.md)

## Cracking with hashcat (GPU)

```bash
# Common modes
#   0    MD5
#   100  SHA1
#   1400 SHA256
#   1700 SHA512
#   1000 NTLM
#   1800 sha512crypt ($6$)
#   3200 bcrypt
#   22000 WPA

hashcat -m 0 hash.txt /usr/share/wordlists/rockyou.txt
hashcat -m 1000 ntlm.txt rockyou.txt
hashcat -m 1800 shadow.txt rockyou.txt -r /usr/share/hashcat/rules/best64.rule

# Show cracked
hashcat -m 0 hash.txt --show
```

→ See [hashcat.md](../tools/hashcat.md)

## Image / file metadata

```bash
file image.jpg                  # true type
exiftool image.jpg              # metadata, Comment, Artist, GPS
exiftool -Comment image.jpg     # single field
strings image.jpg | less        # ascii chunks
strings -n 10 image.jpg         # only long runs
```

## Binary carving

```bash
binwalk image.jpg               # scan for embedded
binwalk -e image.jpg            # extract to _image.jpg.extracted/

foremost -i image.jpg -o carved
# foremost writes carved/jpg, carved/zip, etc.
```

## steghide

```bash
# Info (shows if embedded data exists)
steghide info image.jpg

# Extract with known passphrase
steghide extract -sf image.jpg -p '<PASS>'

# Empty passphrase: just press enter
steghide extract -sf image.jpg
```

## stegcracker — brute force steghide passphrase

```bash
stegcracker image.jpg /usr/share/wordlists/rockyou.txt
```

## zsteg / stegsolve (PNG / BMP)

```bash
zsteg image.png                 # LSB / bitplane dump
zsteg -a image.png              # all checks
stegsolve image.png             # GUI, toggle bit planes
```

## openssl — symmetric encrypt / decrypt

```bash
# Decrypt AES-256-CBC (the CEH classic)
openssl enc -d -aes-256-cbc -in secret.enc -out plain.txt -k '<PASS>'

# If it was made with newer openssl:
openssl enc -d -aes-256-cbc -pbkdf2 -in secret.enc -out plain.txt -k '<PASS>'

# Encrypt
openssl enc -aes-256-cbc -salt -in plain.txt -out secret.enc -k '<PASS>'

# Base64 in/out
openssl enc -d -aes-256-cbc -a -in secret.b64 -out plain.txt -k '<PASS>'
```

## openssl — hashing

```bash
openssl dgst -md5 file
openssl dgst -sha256 file
echo -n 'password' | openssl dgst -md5
```

## GPG

```bash
gpg --decrypt secret.gpg
gpg --output plain --decrypt secret.gpg
```

## Base64 / rot / hex quickies

```bash
base64 -d <<< 'Q0VIe2ZsYWd9'          # decode
echo 'CEH{flag}' | base64             # encode
echo 'uryyb' | tr 'A-Za-z' 'N-ZA-Mn-za-m'   # ROT13
xxd file.bin | less                   # hex dump
```

## Online helpers (Firefox in exam)

- https://gchq.github.io/CyberChef/ — everything
- https://www.dcode.fr — classical ciphers
- https://crackstation.net — hash lookup
- https://md5decrypt.net
