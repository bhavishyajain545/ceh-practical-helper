# 18 Cryptography & Stego — full walkthroughs

---

## Walkthrough 1: "Crack this hash: 5f4dcc3b5aa765d61d8327deb882cf99"

**Type:** hash crack | **Difficulty:** easy | **Time:** <1 min

1. Identify:
   ```bash
   hashid '5f4dcc3b5aa765d61d8327deb882cf99'
   ```
   Output:
   ```
   [+] MD5
   [+] MD4
   [+] NTLM
   ```
   32 hex chars → MD5 is the likely pick.
2. Put it in a file and crack with john:
   ```bash
   echo '5f4dcc3b5aa765d61d8327deb882cf99' > hash.txt
   john --format=raw-md5 --wordlist=/usr/share/wordlists/rockyou.txt hash.txt
   john --show --format=raw-md5 hash.txt
   ```
3. Output:
   ```
   ?:password
   ```
4. **Answer:** `password`

**Faster path:** paste the hash into https://crackstation.net — instant for common hashes.

---

## Walkthrough 2: "Extract the hidden message from image.jpg"

1. Always start with the boring stuff:
   ```bash
   file image.jpg
   exiftool image.jpg
   strings image.jpg | less
   binwalk image.jpg
   ```
2. If `exiftool` shows a `Comment:` field — read it.
3. If `binwalk` shows an embedded ZIP/PNG:
   ```bash
   binwalk -e image.jpg
   ls _image.jpg.extracted/
   ```
4. If nothing obvious, try steghide:
   ```bash
   steghide info image.jpg
   ```
   Prompts for passphrase.
5. If passphrase unknown, brute force:
   ```bash
   stegcracker image.jpg /usr/share/wordlists/rockyou.txt
   ```
   Output:
   ```
   [+] Successfully cracked password: summer2021
   [+] Extracted data written to image.jpg.out
   ```
6. Read it:
   ```bash
   cat image.jpg.out
   ```
7. **Answer:** contents of `image.jpg.out` (usually a flag).

---

## Walkthrough 3: "Decrypt secret.enc — they also gave you password `P@ssw0rd!`"

1. Check the file:
   ```bash
   file secret.enc
   ```
   Likely shows `openssl enc'd data with salted password`.
2. Try the classic AES-256-CBC decrypt:
   ```bash
   openssl enc -d -aes-256-cbc -in secret.enc -out plain.txt -k 'P@ssw0rd!'
   ```
3. If that gives `bad magic number` or `bad decrypt`, the file was encrypted with newer PBKDF2:
   ```bash
   openssl enc -d -aes-256-cbc -pbkdf2 -in secret.enc -out plain.txt -k 'P@ssw0rd!'
   ```
4. Try other algorithms if both fail:
   ```bash
   openssl enc -d -aes-128-cbc -in secret.enc -out plain.txt -k 'P@ssw0rd!'
   openssl enc -d -des-ede3-cbc -in secret.enc -out plain.txt -k 'P@ssw0rd!'
   ```
5. Read result:
   ```bash
   cat plain.txt
   ```
6. **Answer:** contents of `plain.txt`.

---

## Walkthrough 4: "Crack the Linux shadow hash `$6$salt$...`"

1. Identify — `$6$` prefix = sha512crypt.
2. Hashcat mode 1800 (or john format `sha512crypt`):
   ```bash
   hashcat -m 1800 shadow.txt /usr/share/wordlists/rockyou.txt
   ```
   or:
   ```bash
   john --format=sha512crypt --wordlist=/usr/share/wordlists/rockyou.txt shadow.txt
   john --show shadow.txt
   ```
3. **Answer:** the cleartext that john/hashcat recovers.

**Cheat sheet for shadow prefixes:**
- `$1$` → md5crypt  → hashcat `500`
- `$5$` → sha256crypt → hashcat `7400`
- `$6$` → sha512crypt → hashcat `1800`
- `$2a$` / `$2b$` / `$2y$` → bcrypt → hashcat `3200`
- `$y$` → yescrypt (newer)
