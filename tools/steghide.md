# steghide — JPEG/WAV steganography

> **The #1 stego tool in CEH Practical.** If you see a JPEG, BMP, WAV, or AU file and the question mentions "hidden", "embedded", or "secret" — try steghide first. Often pairs with [exiftool](./exiftool.md) and [binwalk](./binwalk.md).

**Install check (Parrot — already installed):** `steghide --version`
**Install if missing:** `sudo apt install steghide`

---

## 🎯 Cheat-flow: "I think there's something hidden in this image"

| Step | Command | Why |
|---|---|---|
| 1. Check metadata first | `exiftool <file>` | Passwords often hide in Comment/Artist/Copyright |
| 2. Check for appended data | `binwalk <file>` | Non-stego hidden files |
| 3. **Try steghide with no password** | `steghide extract -sf <file>` (press Enter at prompt) | Many exam files use blank passphrase |
| 4. Try common passwords | see list below | Exam often uses easy ones |
| 5. Check what metadata steghide sees | `steghide info <file>` | Shows if embedded data exists |
| 6. Brute force with `stegseek` | `stegseek <file> /usr/share/wordlists/rockyou.txt` | Much faster than steghide loops |

**Supported formats:** JPEG, BMP, WAV, AU. **NOT** PNG or GIF — use `zsteg` / `stegsolve` for those.

---

## 🔑 Commands you must know cold

| Command | Meaning |
|---|---|
| `steghide embed -ef <secret> -cf <cover> [-p <pass>]` | **Embed** secret file into cover |
| `steghide extract -sf <stego-file> [-p <pass>]` | **Extract** hidden file |
| `steghide info <file>` | Show capacity and whether data is embedded |
| `steghide --version` | Version check |

### Embed flags
| Flag | Meaning |
|---|---|
| `-ef <file>` | **Embed file** (the secret) |
| `-cf <file>` | **Cover file** (JPEG/BMP/WAV/AU) |
| `-sf <file>` | **Stego file** output (if different from cover) |
| `-p <pass>` | Passphrase (empty string allowed: `-p ""`) |
| `-e <algo>` | Encryption algorithm (default `rijndael-128`) |
| `-z <n>` | Compression level 1–9 |
| `-Z` | No compression |
| `-K` | No encryption |
| `-N` | Don't embed original filename |
| `-f` | Force overwrite |
| `-v` | Verbose |
| `-q` | Quiet |

### Extract flags
| Flag | Meaning |
|---|---|
| `-sf <file>` | Stego file to extract from |
| `-p <pass>` | Passphrase (use `""` for empty) |
| `-xf <file>` | Output filename (overrides original) |
| `-f` | Force overwrite |

---

## 📋 Command recipes (copy-paste)

### The exam "is there anything in this?" flow
```bash
# 0. Always run metadata + binwalk first
exiftool suspicious.jpg
binwalk suspicious.jpg

# 1. Try steghide with EMPTY password (hit Enter)
steghide extract -sf suspicious.jpg
# Enter passphrase: [press Enter]

# 2. Try the explicit empty-string form
steghide extract -sf suspicious.jpg -p ""

# 3. Info (tells you if there IS embedded data, but requires pw)
steghide info suspicious.jpg
```

### Common passphrases to try manually
```
(empty)
password
123456
secret
steghide
admin
letmein
qwerty
<name-from-exiftool-Artist-or-Author>
<filename-without-extension>
<company-name-from-cover>
```

### Brute force with stegseek (much faster)
```bash
# Fastest option — use if steghide manual tries fail
stegseek suspicious.jpg /usr/share/wordlists/rockyou.txt

# Output file lands as suspicious.jpg.out
cat suspicious.jpg.out
```

### Loop over a small wordlist with plain steghide
```bash
while read pw; do
  steghide extract -sf suspicious.jpg -p "$pw" -f 2>/dev/null \
    && echo "[+] PASSWORD: $pw" && break
done < /usr/share/wordlists/fasttrack.txt
```

### Embed a secret (the "reverse" CEH Qs — half of stego questions!)

The typical CEH stego question is split 50/50 between **extract** (given a file, find the secret) and **embed** (given a secret + cover, produce the stego file). Memorize both flows.

```bash
# Canonical embed
steghide embed -cf carrier.jpg -ef secret.txt -p 'password'

# Info — shows capacity, asks if you want to probe (password needed to confirm data)
steghide info carrier.jpg

# Encryption algorithm choice (default rijndael-128 aka AES-128)
steghide embed -cf carrier.jpg -ef secret.txt -p pass -e rijndael-256
steghide embed -cf carrier.jpg -ef secret.txt -p pass -e serpent
steghide embed -cf carrier.jpg -ef secret.txt -p pass -e twofish
steghide embed -cf carrier.jpg -ef secret.txt -p pass -e 3des

# Compression level (0 = no compression, 9 = max; default 9)
steghide embed -cf carrier.jpg -ef secret.txt -p pass -z 9

# List encryption options supported
steghide encinfo

# Embed secret.txt into cover.jpg, output replaces cover
steghide embed -ef secret.txt -cf cover.jpg -p 'mypass'

# Embed with empty passphrase
steghide embed -ef secret.txt -cf cover.jpg -p ""

# Embed into a WAV
steghide embed -ef notes.txt -cf audio.wav -p 'mypass'

# Embed without filename hint and without compression
steghide embed -ef secret.txt -cf cover.jpg -p 'mypass' -N -Z
```

### Show capacity and check for embedded data
```bash
steghide info cover.jpg
# → capacity: 4.3 KB
# → "Try to get information about embedded data? (y/n)" → y
# → prompts for password; if correct, shows file name + size
```

---

## ⚠️ Gotchas

- **Steghide only supports JPEG, BMP, WAV, AU.** For PNG try `zsteg`, for GIF try `stegsolve`, for text try `stegsnow`.
- **The passphrase is often EMPTY.** Always try `-p ""` or pressing Enter first before wordlists.
- **Check metadata BEFORE brute-forcing.** Run `exiftool` — the password is frequently sitting in the Comment, Author, Copyright, or Keywords field.
- **`steghide info` asks for the password** to confirm embedded data. Without the pw it only tells you capacity, not whether anything is hidden.
- **Extracted file lands in CWD** with the original filename. If it overwrites something, use `-xf newname`.
- **Binwalk vs steghide are different techniques.** Binwalk finds appended/concatenated data; steghide uses LSB embedding with encryption. Run both.
- **JPEG re-saved after embedding?** Embedded data is destroyed. Don't open & re-save the file before extraction.
- **stegseek is 1000x faster** than a steghide bash loop against rockyou. Use it if available.
- **Answer format:** If the Q asks for the secret content, `cat` the extracted file and copy exactly — watch trailing newlines.

---

## 🔗 Related

- [exiftool — check metadata for the password](./exiftool.md)
- [binwalk — find appended/embedded files](./binwalk.md)
- [foremost — carve files from larger blobs](./foremost.md)
- [Steganography question bank](../questions/by-domain/18-cryptography.md)
