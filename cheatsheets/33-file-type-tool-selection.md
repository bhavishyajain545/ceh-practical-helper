# FILE MILI — KAUNSA TOOL USE KARU?

---

## Ye Kya Hai?
Exam mein tujhe koi unknown file milegi — image, audio, archive, network capture, ya kuch
aur. Tujhe samajhna hai ye file kya hai aur kaunse tool se analyse karna hai. Ye cheatsheet
har file type ke liye exact commands degi.

**Golden Rule:** Pehle 5 universal steps karo, phir file type ke basis pe specific tool use karo.

---

## UNIVERSAL FIRST STEPS — KOI BHI FILE MILE, PEHLE YE KARO

```bash
# STEP 1: File type identify karo
file mystery_file
# Output: "JPEG image data" ya "PNG image" ya "Zip archive" etc.
# Ye magic bytes (file ke starting bytes) se type batata hai

# STEP 2: Metadata check karo
exiftool mystery_file
# Hidden comments, GPS location, author name, software used — sab dikhta hai
# FLAG ya PASSWORD yahan chhupa sakta hai!

# STEP 3: Quick string search
strings mystery_file | grep -i -E "flag|pass|key|secret|ctf|hack"
# File ke andar readable text dhundho — kabhi kabhi seedha flag milta hai

# STEP 4: Embedded files check karo
binwalk mystery_file
# Kya file ke andar doosri files chhupai hain? (zip inside image, etc.)
binwalk -e mystery_file             # Extract embedded files

# STEP 5: Hex dump dekho (first few bytes)
xxd mystery_file | head -5
# Magic bytes dekho — file type confirm karo
```

### Common Magic Bytes (Hex mein):
| Hex Start | File Type |
|---|---|
| `FF D8 FF` | JPEG image |
| `89 50 4E 47` | PNG image |
| `47 49 46 38` | GIF image |
| `50 4B 03 04` | ZIP archive (aur .docx, .xlsx, .apk, .jar bhi!) |
| `52 61 72 21` | RAR archive |
| `25 50 44 46` | PDF file |
| `7F 45 4C 46` | ELF binary (Linux executable) |
| `4D 5A` | PE/EXE (Windows executable) |
| `D0 CF 11 E0` | Microsoft Office (old .doc/.xls) |

**Kyu important hai:** Kabhi kabhi file ka extension galat hota hai (flag.jpg actually ZIP hai).
Magic bytes se real type pata chalta hai.

---

## IMAGE FILES — Steganography Check Karo

### JPEG (.jpg / .jpeg)
```bash
# Steghide — JPEG mein hidden data extract karo
steghide extract -sf image.jpg
# Password poochega — Enter press karo (empty password try)
# Ya known password daalo: steghide extract -sf image.jpg -p "password"

# Stegseek — Steghide ka brute force version (FAST)
stegseek image.jpg /usr/share/wordlists/rockyou.txt
# Rockyou se password try karke extract karega automatically

# Exiftool — Metadata mein kuch chhupa hai?
exiftool image.jpg
# Comment, Description, XPComment fields mein flag dekho

# Strings — Readable text
strings image.jpg | grep -i flag

# Binwalk — File ke andar file?
binwalk -e image.jpg
```

### PNG (.png)
```bash
# Zsteg — PNG/BMP ke liye best steganography tool
zsteg image.png
# Multiple techniques try karta hai — LSB, channels, etc.
# Output mein readable text dikhe = found it!

# Zsteg detailed:
zsteg -a image.png                  # ALL possible techniques try karo

# Pngcheck — PNG corruption check
pngcheck -v image.png

# Stegoveritas — Advanced (slow but thorough)
stegoveritas image.png
```

### BMP (.bmp)
```bash
zsteg image.bmp                     # Same as PNG
steghide extract -sf image.bmp      # Steghide BMP bhi support karta hai
```

### Visual Analysis (Agar automated tools se kuch nahi mila):
```bash
# Stegsolve — Visual analysis (different color planes)
java -jar stegsolve.jar
# Open image → Arrow buttons se different planes dekho
# Red plane 0, Green plane 0, Blue plane 0 mein hidden images dikhte hain
```

---

## AUDIO FILES

| Extension | Tool | Command |
|---|---|---|
| `.wav` | steghide | `steghide extract -sf audio.wav` |
| `.wav` | sonic-visualiser | Open → Add Spectrogram → message dikhega |
| `.wav` | Audacity | Open → Spectrogram view enable karo |
| `.mp3` | strings | `strings audio.mp3 \| grep -i flag` |
| `.mp3` | binwalk | `binwalk -e audio.mp3` |
| `.au` | steghide | `steghide extract -sf audio.au` |

**Spectrogram kya hai:** Audio ko visual form mein dekhna — kabhi kabhi wahan text ya QR code
hidden hota hai. Sonic Visualiser ya Audacity mein spectrogram view enable karo.

---

## TEXT / ENCODED FILES

```bash
# Base64 encoded (looks like random alphanumeric with = at end):
base64 -d file.txt

# Hex encoded:
xxd -r -p file.txt

# ROT13:
cat file.txt | tr 'A-Za-z' 'N-ZA-Mn-za-m'

# Whitespace steganography (tab/space hidden message):
snow -C file.txt
# Ya: stegsnow -C file.txt

# Binary (0s and 1s):
# CyberChef mein paste karo → "From Binary" operation use karo

# Morse code (dots and dashes):
# CyberChef → "From Morse Code"
```

**Encoding vs Encryption:**
- Encoding = Reversible without key (Base64, Hex, URL encoding)
- Encryption = Key chahiye (AES, RSA, etc.)

---

## ENCRYPTED FILES

| Extension | What | Command |
|---|---|---|
| `.enc` | OpenSSL encrypted | `openssl enc -aes-256-cbc -d -in file.enc -out decrypted.txt` |
| `.gpg` | GPG encrypted | `gpg -d file.gpg` (key chahiye) |
| `.hc` | VeraCrypt volume | `veracrypt --text file.hc /mnt/vc` |
| `.kdbx` | KeePass database | `keepass2john file.kdbx > hash.txt && john hash.txt` |
| `.pfx` / `.p12` | Certificate | `openssl pkcs12 -in file.pfx -out cert.pem` |

---

## ARCHIVE FILES (Password Protected)

**Pattern same hai: file2john se hash nikalo → john se crack karo**

```bash
# ZIP:
zip2john protected.zip > hash.txt
john --wordlist=/usr/share/wordlists/rockyou.txt hash.txt
john --show hash.txt                # Password dekho
unzip protected.zip                 # Password se extract

# RAR:
rar2john protected.rar > hash.txt
john --wordlist=/usr/share/wordlists/rockyou.txt hash.txt

# 7Z:
7z2john.pl protected.7z > hash.txt
john --wordlist=/usr/share/wordlists/rockyou.txt hash.txt

# PDF (password protected):
pdf2john.pl protected.pdf > hash.txt
john --wordlist=/usr/share/wordlists/rockyou.txt hash.txt

# Office (.docx/.xlsx — password protected):
office2john.py protected.docx > hash.txt
john --wordlist=/usr/share/wordlists/rockyou.txt hash.txt
```

---

## NETWORK CAPTURE FILES

```bash
# PCAP / PCAPNG — Wireshark se open karo
wireshark file.pcap

# Wireshark mein kya dekho:
# 1. File → Export Objects → HTTP (downloaded files extract)
# 2. Follow TCP Stream (right-click any packet → Follow → TCP)
# 3. Filter: http.request.method == "POST" (login creds)
# 4. Filter: ftp.request.command == "PASS" (FTP passwords)
# 5. Filter: dns (DNS queries)

# Tshark (command line Wireshark):
tshark -r file.pcap -Y "http" -T fields -e http.request.uri
tshark -r file.pcap -Y "ftp" -T fields -e ftp.request.arg

# WiFi Capture (.cap):
aircrack-ng -w /usr/share/wordlists/rockyou.txt wifi.cap
# WPA handshake crack karega
```

---

## ANDROID FILES

```bash
# APK — Android app decompile
apktool d app.apk -o app_decompiled/
# AndroidManifest.xml, resources, smali code milega

# APK — Java source code decompile
jadx -d output/ app.apk
# Readable Java code milega — hardcoded credentials dhundho

# SQLite Database:
sqlite3 database.db
sqlite> .tables                     # Table list
sqlite> SELECT * FROM users;        # Data dump
sqlite> .quit
```

---

## BINARY / EXECUTABLE FILES

```bash
# Linux ELF:
file binary_file                    # Confirm ELF
chmod +x binary_file
strings binary_file | grep -i flag  # Strings search
ltrace ./binary_file                # Library call trace
strace ./binary_file                # System call trace
gdb ./binary_file                   # Debugger

# Windows EXE/DLL:
file binary.exe                     # Confirm PE
strings binary.exe | grep -i flag
# Ghidra ya IDA Free mein open karo for reverse engineering
```

---

## Common Mistakes (Mat Karna Ye)

1. **`file` command skip karna** — Extension pe trust mat karo, `file` command se verify karo
2. **Empty password try nahi karna** — steghide mein Enter press karo pehle (empty pass)
3. **Stegseek nahi use karna JPEG pe** — steghide se ek ek password try karna slow hai
4. **ZIP file ko ZIP samajhna** — `.docx`, `.apk`, `.jar` bhi ZIP format hote hain!
5. **Binwalk skip karna** — Image ke andar ZIP/RAR chhupa hota hai aksar
6. **Sirf ek tool try karna** — Ek nahi mila toh doosra try karo
7. **strings mein grep nahi lagana** — Output bahut bada hota hai, filter karo

---

## Quick Decision Tree

```
Unknown file mila
  |
  +-- file mystery_file --> type pata chala
  +-- exiftool mystery_file --> metadata check
  +-- strings mystery_file | grep flag --> quick win
  +-- binwalk -e mystery_file --> embedded files
  |
  +-- Image?
  |     +-- JPEG --> steghide → stegseek → exiftool
  |     +-- PNG --> zsteg → pngcheck → stegoveritas
  |     +-- BMP --> zsteg → steghide
  |
  +-- Audio?
  |     +-- WAV --> steghide → spectrogram
  |     +-- MP3 --> strings → binwalk
  |
  +-- Archive?
  |     +-- Password protected --> file2john → john → extract
  |     +-- No password --> unzip/unrar → contents check
  |
  +-- Network?
  |     +-- PCAP --> Wireshark → Export Objects → Follow Stream
  |     +-- CAP (WiFi) --> aircrack-ng
  |
  +-- Text?
  |     +-- Looks Base64 --> base64 -d
  |     +-- Whitespace --> snow -C
  |
  +-- Android?
  |     +-- APK --> apktool + jadx → hardcoded creds
  |     +-- DB --> sqlite3 → tables dump
  |
  +-- Binary?
        +-- strings → ltrace → strace → gdb/ghidra
```
