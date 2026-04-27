# 🖼️ IMAGE/FILE SUSPICIOUS HAI — Hidden Data Nikalo

---

## STEP 1: File Type Confirm Karo
```bash
file suspicious_file
xxd suspicious_file | head -3              # magic bytes
exiftool suspicious_file                   # metadata
```

---

## STEP 2: File Type Ke Hisaab Se Tool Choose Karo

| File Type | Primary Tool | Command |
|---|---|---|
| **JPEG** | steghide | `steghide extract -sf image.jpg` |
| **PNG** | zsteg | `zsteg image.png` |
| **BMP** | zsteg / steghide | `zsteg image.bmp` |
| **WAV/AU** | steghide | `steghide extract -sf audio.wav` |
| **Text file** | snow | `snow -C file.txt` |
| **Any file** | binwalk | `binwalk -e file` |
| **Any file** | strings | `strings file \| grep -i flag` |

---

## CASE 1: JPEG — Steghide
```bash
# Bina password
steghide extract -sf image.jpg
# Password ke saath
steghide extract -sf image.jpg -p "password"
# Password nahi pata? Crack karo:
stegseek image.jpg /usr/share/wordlists/rockyou.txt
```
> Password kahan hoga? → EXIF metadata mein, question text mein, ya dusri file mein

---

## CASE 2: PNG — zsteg
```bash
zsteg image.png                            # auto scan all channels
zsteg -a image.png                         # thorough scan
zsteg -E "b1,rgb,lsb" image.png           # extract specific channel
```
> zsteg pe steghide MAT use karo — "unsupported format" aayega

---

## CASE 3: Text File — Snow (Whitespace Stego)
```bash
snow -C file.txt                           # no password
snow -C -p "password" file.txt             # with password
cat -A file.txt                            # trailing spaces dikhenge
```

---

## CASE 4: Binwalk — Embedded Files
```bash
binwalk suspicious_file                    # scan karo
binwalk -e suspicious_file                 # extract karo
ls _suspicious_file.extracted/             # extracted files
```
> ZIP, PDF, image — kuch bhi embedded ho sakta hai

---

## CASE 5: EXIF Metadata Mein Hidden
```bash
exiftool image.jpg                         # sab fields
exiftool -Comment image.jpg                # comment field
exiftool -GPSLatitude -GPSLongitude image.jpg  # GPS coords
```

---

## CASE 6: Strings — Quick Check
```bash
strings file | grep -i "flag\|password\|secret\|CEH"
strings file | tail -20                    # end mein appended data
```

---

## CASE 7: Hex Analysis — EOF Ke Baad Data
```bash
xxd image.jpg | tail -20                   # JPEG ends at FF D9
strings image.jpg | tail -5               # appended text
```

---

## CASE 8: Stegsolve — Bit Plane Analysis
```bash
java -jar stegsolve.jar
# File → Open → Arrow keys se planes cycle karo
# Red/Green/Blue plane 0 mein hidden text/QR dhundo
```

---

## CASE 9: Audio Steganography
```bash
steghide extract -sf audio.wav -p "password"
# Spectogram analysis:
sonic-visualiser audio.wav                 # agar installed ho
```

---

## CASE 10: Multi-Layer Stego
```bash
# Layer 1: Binwalk extract
binwalk -e outer_file
# Layer 2: Steghide on extracted file
steghide extract -sf extracted_image.jpg -p "pass"
# Layer 3: Base64 decode
cat hidden.txt | base64 -d
```

---

## QUICK DECISION:
```
Suspicious file mili
  ├─ file type check: file + xxd
  ├─ JPEG? → steghide extract (try empty pass first)
  │         → stegseek crack (agar pass nahi pata)
  ├─ PNG?  → zsteg (auto detect LSB)
  ├─ Text? → snow -C (whitespace stego)
  ├─ Any?  → binwalk -e (embedded files)
  │         → strings | grep flag (quick check)
  │         → exiftool (metadata mein hidden?)
  ├─ Password kahan? → EXIF comment, question text, another file
  └─ Kuch nahi mila? → stegsolve (visual bit plane analysis)
```
