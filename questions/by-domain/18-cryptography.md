# Question Bank — Cryptography & Steganography (14 questions)

> Heavily tested on CEH Practical. Expect 3-5 questions from this domain. Have hashid, john, hashcat, steghide, exiftool, binwalk, openssl ready.

**Legend:** 🟢 easy · 🟡 medium · 🔴 hard

---

### Q1 🟢 — Identify the hash type of `5f4dcc3b5aa765d61d8327deb882cf99`

**Category:** Hash ID | **Tools:** [hashid](../../tools/hashid.md)

**Steps:**
1. `echo -n '5f4dcc3b5aa765d61d8327deb882cf99' | hashid` ← [why hashid](../../tools/hashid.md#usage)
2. Or by length: 32 hex = MD5/NTLM/MD4 (rule out by context).

**Answer format:** `MD5` (and hashcat mode `0`, john format `raw-md5`).

**Gotcha:** hashid prints multiple possibilities — use length + context (NTLM comes from Windows dumps, MD5 from web apps).

---

### Q2 🟢 — Common hash length cheat sheet (reference Q)

**Category:** Hash ID | **Tools:** memory

**Steps:**
1. 32 hex = MD5 or NTLM (hashcat `0` / `1000`)
2. 40 hex = SHA-1 (hashcat `100`)
3. 64 hex = SHA-256 (hashcat `1400`)
4. 128 hex = SHA-512 (hashcat `1700`)
5. `$2a$` / `$2b$` / `$2y$` = bcrypt (hashcat `3200`) — SLOW
6. `$1$` = md5crypt, `$6$` = sha512crypt (hashcat `1800`)
7. `user:RID:LM:NTLM:::` = pwdump format
8. 13 chars `ab$xxxxxxxxxxx` = DES crypt

**Answer format:** N/A — mental reference.

---

### Q3 🟢 — Crack an MD5 hash with john

**Category:** Hash cracking | **Tools:** [john](../../tools/john.md)

**Steps:**
1. Save hash to `hash.txt` (just the hash, one line).
2. `john --format=raw-md5 --wordlist=/usr/share/wordlists/rockyou.txt hash.txt` ← [why --format](../../tools/john.md#formats)
3. `john --show --format=raw-md5 hash.txt`

**Answer format:** the cleartext password.

**Gotcha:** `rockyou.txt` is at `/usr/share/wordlists/rockyou.txt.gz` on Kali — `gunzip` it once before the exam.

---

### Q4 🟢 — Crack an MD5 hash with hashcat

**Category:** Hash cracking | **Tools:** [hashcat](../../tools/hashcat.md)

**Steps:**
1. `hashcat -m 0 -a 0 hash.txt /usr/share/wordlists/rockyou.txt` ← [why -m 0](../../tools/hashcat.md#modes)
2. `hashcat -m 0 hash.txt --show`

**Answer format:** the cleartext password.

**Gotcha:** exam VMs often lack GPUs — add `--force` and expect CPU speeds (~10M H/s for MD5). Stick to rockyou, don't waste time on rules.

---

### Q5 🟡 — Crack a SHA-1 hash

**Category:** Hash cracking | **Tools:** [hashcat](../../tools/hashcat.md), [john](../../tools/john.md)

**Steps:**
1. Confirm: `echo -n '<hash>' | hashid` -> SHA-1 (40 hex).
2. hashcat: `hashcat -m 100 -a 0 hash.txt rockyou.txt`
3. john: `john --format=raw-sha1 --wordlist=rockyou.txt hash.txt`

**Answer format:** cleartext password.

---

### Q6 🟡 — Crack an NTLM hash from a Windows dump

**Category:** Hash cracking | **Tools:** [hashcat](../../tools/hashcat.md)

**Steps:**
1. Extract just the NTLM column if given pwdump: `cut -d: -f4 dump.txt > ntlm.txt`
2. `hashcat -m 1000 -a 0 ntlm.txt /usr/share/wordlists/rockyou.txt`
3. `hashcat -m 1000 ntlm.txt --show`

**Answer format:** `user:password` or just the password, as asked.

**Gotcha:** NTLM is unsalted MD4 — crazy fast. If it's not in rockyou after 30s, the answer isn't in rockyou; add `-r /usr/share/hashcat/rules/best64.rule`.

---

### Q7 🟢 — Extract hidden text from `secret.jpg` with steghide (no password)

**Category:** Stego | **Tools:** [steghide](../../tools/steghide.md)

**Steps:**
1. `steghide info secret.jpg` ← [why info](../../tools/steghide.md#info)
2. `steghide extract -sf secret.jpg` — press Enter for empty password.
3. `cat <extracted_file>`

**Answer format:** the extracted string / file contents.

**Gotcha:** steghide only works on JPG/BMP/WAV/AU. For PNG/GIF try `zsteg` / `stegsolve` instead.

---

### Q8 🟡 — Brute-force the steghide password on `secret.jpg`

**Category:** Stego | **Tools:** `stegcracker`, [steghide](../../tools/steghide.md)

**Steps:**
1. `stegcracker secret.jpg /usr/share/wordlists/rockyou.txt`
2. When found, `steghide extract -sf secret.jpg -p <password>`

**Answer format:** extracted file / string.

**Gotcha:** stegcracker wraps steghide in a bash loop — it's slow (~100/s). If rockyou doesn't hit in 5 minutes, the password isn't in it.

---

### Q9 🟢 — Extract metadata (author, GPS) from `photo.jpg`

**Category:** Metadata | **Tools:** [exiftool](../../tools/exiftool.md)

**Steps:**
1. `exiftool photo.jpg` — dumps all tags.
2. For targeted fields:
   ```bash
   exiftool -Author -Artist -Creator -GPSLatitude -GPSLongitude photo.jpg
   ```

**Answer format:** the author name, or lat/long as printed.

**Gotcha:** CEH loves `Author`, `Comment`, `Software`, and `GPSPosition`. GPS is in DMS format by default — add `-c "%.6f"` for decimal.

---

### Q10 🟡 — Carve hidden files from `cover.png` with binwalk

**Category:** File carving | **Tools:** [binwalk](../../tools/binwalk.md), `foremost`

**Steps:**
1. `binwalk cover.png` — shows embedded signatures.
2. `binwalk -e cover.png` — extracts into `_cover.png.extracted/`.
3. Fallback: `foremost -i cover.png -o out/`.
4. Check entropy for encrypted chunks: `binwalk -E cover.png`.

**Answer format:** extracted filename / its contents.

**Gotcha:** `-e` sometimes fails silently on newer binwalk — `binwalk --dd='.*' cover.png` forces extract-all.

---

### Q11 🟡 — Decrypt an AES-CBC file given key and IV

**Category:** Symmetric decrypt | **Tools:** [openssl](../../tools/openssl.md)

**Steps:**
1. Confirm the cipher from the question (AES-128-CBC / AES-256-CBC).
2. Key and IV are usually hex strings:
   ```bash
   openssl enc -d -aes-256-cbc \
     -K <key_hex> -iv <iv_hex> \
     -in cipher.bin -out plain.txt
   ```
3. `cat plain.txt`

**Answer format:** plaintext contents.

**Gotcha:** `-K` (uppercase) takes hex; `-k` (lowercase) is a passphrase. Mixing them up is the #1 mistake. Also add `-nopad` if output looks truncated.

---

### Q12 🟢 — Decode a base64 string

**Category:** Encoding | **Tools:** `base64`, `xxd`

**Steps:**
1. `echo '<string>' | base64 -d`
2. If binary, pipe to `xxd` or `file -`.
3. Often chained: base64 -> gzip -> text. Try `base64 -d | gunzip`.

**Answer format:** the decoded string.

**Gotcha:** URL-safe base64 uses `-_` instead of `+/`. Convert first: `tr '_-' '/+' | base64 -d`.

---

### Q13 🟡 — Find hidden ASCII data inside a binary with `strings`

**Category:** Static strings | **Tools:** `strings`, `grep`

**Steps:**
1. `strings -n 8 suspect.bin | less`
2. Target keywords: `strings suspect.bin | grep -iE "flag|pass|http|key|CTF"`
3. For wide/UTF-16 strings: `strings -e l suspect.bin`

**Answer format:** the hidden string.

**Gotcha:** Windows binaries store strings as UTF-16 — always also run `strings -e l`. Missing this loses points.

---

### Q14 🟡 — Decode a ROT13 / Caesar cipher / identify Vigenere

**Category:** Classical ciphers | **Tools:** `tr`, CyberChef

**Steps:**
1. ROT13: `echo 'Uryyb' | tr 'A-Za-z' 'N-ZA-Mn-za-m'`
2. Caesar brute (all 25 shifts):
   ```bash
   for i in $(seq 1 25); do echo "$i: $(echo 'CIPHER' | caesar $i)"; done
   ```
3. Identify cipher type:
   - Single alphabet, letter freq skewed -> Caesar / substitution.
   - Letter freq flat, repeating patterns at intervals -> Vigenere (use `dcode.fr` or `vigenere-solver`).
   - Only `A-F0-9` -> hex. Only `A-Z2-7=` -> base32.
4. Substitution: `quipqiup.com` (offline: write a freq-analysis script).

**Answer format:** the plaintext.

**Gotcha:** CEH "cryptanalysis" Qs are almost always Caesar, ROT13, base64, or hex. Don't overthink — brute the easy ones first.

---

## 📌 Quick links

- [hashid reference](../../tools/hashid.md)
- [john the ripper reference](../../tools/john.md)
- [hashcat reference](../../tools/hashcat.md)
- [steghide reference](../../tools/steghide.md)
- [exiftool reference](../../tools/exiftool.md)
- [binwalk reference](../../tools/binwalk.md)
- [openssl reference](../../tools/openssl.md)
- [18-cryptography README](../../18-cryptography/README.md)
