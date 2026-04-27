# IMAGE/FILE SUSPICIOUS HAI — Hidden Data Nikalo

---

## Pehle Samjho: Steganography Kya Hai?

Steganography = **Data ko kisi aur file ke andar chhupana** — aise ki dekhne wale ko pata hi na chale.

**Encryption vs Steganography:**
- Encryption: Data ko unreadable bana deta hai (sabko pata hai ki kuch chhupa hai)
- Steganography: Data ko kisi normal file mein chhupa deta hai (kisiko pata hi nahi chalta)

**Kaise chhupate hain?**
- **LSB (Least Significant Bit):** Image ke har pixel ka last bit change karke data store karte hain.
  Human eye ko fark nazar nahi aata, lekin tools se nikal sakte ho.
- **EOF (End of File):** File ke end ke baad extra data append kar dete hain.
  File normal dikhai deti hai, lekin andar extra data hai.
- **Metadata:** EXIF fields mein text chhupa dete hain (Comment, Author, etc.)
- **Whitespace:** Text file mein invisible spaces/tabs se data encode karte hain

**CEH mein kya milega?**
- Image file (JPEG/PNG) with hidden text/file
- Password-protected steghide extraction
- Multi-layer stego (image ke andar image ke andar text)
- Audio file mein hidden data

---

## STEP 1: File Type Confirm Karo (Bahut Important!)

Kabhi kabhi file ka extension galat hota hai — `.jpg` likha hai lekin actually PNG hai.

```bash
# file command — actual type batata hai (magic bytes se)
file suspicious_file
# Output: JPEG image data, JFIF standard
# Ya: PNG image data, 800 x 600
# Ya: Zip archive data

# xxd — hex dump dekho (first few bytes = magic bytes)
xxd suspicious_file | head -3
# JPEG: FF D8 FF se start hota hai
# PNG:  89 50 4E 47 (.PNG) se start hota hai
# ZIP:  50 4B 03 04 (PK..) se start hota hai
# PDF:  25 50 44 46 (%PDF) se start hota hai
# GIF:  47 49 46 38 (GIF8) se start hota hai

# exiftool — saari metadata ek saath
exiftool suspicious_file
# File type, dimensions, camera info, GPS, comments — sab dikhega
```

> **Kyun check karna zaroori hai?**
> Kyunki tool selection file type pe depend karti hai!
> JPEG pe zsteg nahi chalega, PNG pe steghide nahi chalega.
> Galat tool = "unsupported format" error = time waste.

---

## STEP 2: File Type Ke Hisaab Se Tool Select Karo

| File Type | Primary Tool | Kaise Use Karein | Notes |
|---|---|---|---|
| **JPEG/JPG** | steghide | `steghide extract -sf image.jpg` | Password maang sakta hai |
| **JPEG** (pass unknown) | stegseek | `stegseek image.jpg rockyou.txt` | Password crack karta hai |
| **PNG** | zsteg | `zsteg image.png` | LSB stego detect karta hai |
| **BMP** | zsteg | `zsteg image.bmp` | PNG jaisa hi |
| **WAV/AU** | steghide | `steghide extract -sf audio.wav` | Audio stego |
| **Text file** | snow | `snow -C file.txt` | Whitespace stego |
| **Any file** | binwalk | `binwalk -e file` | Embedded files nikalta hai |
| **Any file** | strings | `strings file \| grep -i flag` | Quick text search |
| **Any image** | stegsolve | `java -jar stegsolve.jar` | Visual bit plane analysis |

---

## CASE 1: JPEG File — Steghide Se Extract Karo

Steghide JPEG aur WAV files ke liye sabse common tool hai.

```bash
# TRY 1: Bina password (empty passphrase)
steghide extract -sf image.jpg
# Agar password nahi lagaya tha toh seedha extract ho jayega
# "Enter passphrase:" aaye toh just Enter press karo (empty)

# TRY 2: Password ke saath (agar pata hai)
steghide extract -sf image.jpg -p "secretpass"
# -p = passphrase
# Output: wrote extracted data to "hidden.txt"

# TRY 3: Steghide info — pehle dekho kya chhupa hai
steghide info image.jpg
# Ye batayega: embedded file name, encryption algorithm, compressed size
# "embedded file: flag.txt" — matlab flag.txt chhupa hai
```

**Password kahan milega?**
- Question text mein hint hoga
- EXIF metadata mein (Comment field check karo)
- Dusri file mein (agar multiple files di hain)
- File name mein hint ho sakta hai

**Agar password pata nahi hai — Stegseek se crack karo:**
```bash
stegseek image.jpg /usr/share/wordlists/rockyou.txt
# Ye bahut fast hai — seconds mein crack ho jaata hai
# Output: Found passphrase: "password123"
# Extracted file automatically save ho jaati hai

# Stegseek nahi hai? Install karo:
# apt install stegseek
```

> **Output file kahan jayegi?** Same directory mein — `image.jpg.out` ya jo embedded filename hai.

---

## CASE 2: PNG File — zsteg Se Scan Karo

PNG files ke liye zsteg best hai — ye LSB steganography detect karta hai.

```bash
# Auto scan — saare channels check karo
zsteg image.png
# Output mein har channel ka data dikhega:
# b1,rgb,lsb,xy .. text: "FLAG{hidden_data}"  ← ye mil gaya!
# b1,r,lsb,xy   .. file: PNG image data       ← embedded image
# b2,g,msb,xy   .. [random gibberish]          ← ye kuch nahi hai

# Thorough scan (zyada channels)
zsteg -a image.png
# -a = all combinations try karo (thoda slow lekin thorough)

# Specific channel se extract karo
zsteg -E "b1,rgb,lsb" image.png > extracted_data
# Agar pata hai kaunse channel mein hai toh directly extract karo
```

**Output kaise samjhein:**
- `text:` ke baad readable text dikhe → **ye answer hai!**
- `file:` ke baad file type dikhe → embedded file hai, extract karo
- Random characters / gibberish → ignore karo, ye noise hai

> **IMPORTANT:** PNG pe steghide mat use karo — "unsupported format" error aayega!
> Steghide sirf JPEG aur WAV support karta hai.

---

## CASE 3: Text File — Snow (Whitespace Steganography)

Snow tool whitespace (spaces aur tabs) mein data chhupata hai. Dekhne mein file normal lagti hai.

```bash
# Extract hidden data (bina password)
snow -C file.txt
# -C = extract mode
# Output: SECRET_MESSAGE

# Password ke saath
snow -C -p "password" file.txt

# Confirm karo ki whitespace stego hai:
cat -A file.txt
# $ ke pehle extra spaces dikhenge → whitespace stego hai!
# Example: "Normal text here     $"
#           ye extra spaces ↑ = hidden data
```

> **Kaise pata chalega ki Snow stego hai?**
> - `cat -A` se trailing whitespace dikhe
> - File ka size zyada hai normal text ke liye
> - Question mein "whitespace" ya "snow" ka hint ho

---

## CASE 4: Binwalk — Kisi Bhi File Mein Embedded Files Dhundo

Binwalk bahut powerful hai — ye file ke andar chhupe files detect aur extract karta hai.

```bash
# STEP 1: Scan karo — kya hai andar?
binwalk suspicious_file
# Output:
# DECIMAL    HEXADECIMAL  DESCRIPTION
# 0          0x0          JPEG image data
# 45678      0xB26E       Zip archive data    ← ZIP chhupa hai!
# 45890      0xB342       End of Zip archive

# STEP 2: Extract karo
binwalk -e suspicious_file
# -e = extract
# Ek folder banega: _suspicious_file.extracted/

# STEP 3: Extracted files dekho
ls _suspicious_file.extracted/
# Andar se ZIP, PDF, image — kuch bhi nikal sakta hai
# Us extracted file ko bhi check karo (multi-layer stego!)
```

**Binwalk kab use karein?**
- Jab bhi file size suspicious lage (image 5MB ki hai but simple photo hai?)
- Jab steghide/zsteg se kuch na mile
- Jab file ka type hi confirm nahi ho raha

> **Pro tip:** Binwalk har file pe try karo — ye sabse universal tool hai.
> Kuch nahi mila toh bhi koi loss nahi.

---

## CASE 5: EXIF Metadata Mein Hidden Data

Kabhi kabhi answer EXIF metadata mein hota hai — specially Comment, Artist, Copyright fields mein.

```bash
# Saari metadata dekho
exiftool image.jpg
# Dhyan se dekho ye fields:
# Comment       : FLAG{metadata_is_fun}     ← ye dekho!
# Artist        : admin:password123
# Copyright     : secret_key_here
# GPS Latitude  : 28.6139 N                 ← location bhi ho sakta
# GPS Longitude : 77.2090 E

# Specific field extract karo
exiftool -Comment image.jpg
# Output: FLAG{metadata_is_fun}

# GPS coordinates nikalo
exiftool -GPSLatitude -GPSLongitude image.jpg
# Google Maps mein daal ke location dhundo
```

> **Har image pe exiftool zaroor chalao** — 2 second lagta hai aur kabhi kabhi
> seedha answer mil jaata hai bina kisi complex tool ke.

---

## CASE 6: Strings — Quick and Dirty Check

Strings command file mein readable text dhundti hai. Sabse pehle ye try karo!

```bash
# Flag/password/secret dhundo
strings file | grep -i "flag\|password\|secret\|CEH\|key"
# Agar mil gaya toh baaki tools ki zaroorat hi nahi!

# File ke end mein appended text (EOF stego)
strings file | tail -20
# Kabhi kabhi file ke end mein text append kiya hota hai

# Longer strings only (noise kam karne ke liye)
strings -n 10 file | head -30
# -n 10 = minimum 10 characters ke strings hi dikhao
```

> **Strings kab use karein?** HAMESHA pehle! Ye 1 second lagta hai.
> 50% cases mein strings se hi answer mil jaata hai.

---

## CASE 7: Hex Analysis — EOF Ke Baad Data

Kabhi kabhi file ke end ke baad extra data append hota hai.

```bash
# JPEG file: FF D9 ke baad kuch nahi hona chahiye
xxd image.jpg | tail -20
# Agar FF D9 (JPEG end marker) ke baad aur data hai → hidden data!

# PNG file: IEND ke baad
xxd image.png | tail -20
# 49 45 4E 44 (IEND) ke baad data = appended data

# Appended text directly nikalo
strings image.jpg | tail -5
# Jo JPEG end ke baad ka text hai wo yahan dikhega
```

---

## CASE 8: Stegsolve — Visual Bit Plane Analysis

Jab kuch nahi mil raha, toh stegsolve se image ki different bit planes dekho.

```bash
# Stegsolve run karo (Java app hai)
java -jar /path/to/stegsolve.jar
# File → Open → Image select karo
```

**Stegsolve mein kya karna hai:**
1. Arrow keys (< >) se bit planes cycle karo
2. Har plane mein dekho — koi text ya QR code chhupa ho sakta hai
3. **Red plane 0, Green plane 0, Blue plane 0** — ye sabse common hiding spots hain
4. Agar QR code dikhe → screenshot lo → QR reader se scan karo

**Important planes:**
- Red 0, Green 0, Blue 0 — LSB planes (sabse common)
- Alpha 0 — transparency channel
- "Random" looking plane mein pattern dikhe → data hai!

---

## CASE 9: Audio Steganography

Audio files mein bhi data chhupa hota hai — specially WAV files mein.

```bash
# WAV file — steghide se extract karo
steghide extract -sf audio.wav
steghide extract -sf audio.wav -p "password"

# Spectrogram analysis — visual message
# Sonic Visualiser ya Audacity use karo
# Audacity: File → Import → Audio → audio.wav
# Then: Analyze → Plot Spectrum
# Ya: View → Spectrogram
# Spectrogram mein text/image chhupa hota hai kabhi kabhi!

# strings bhi try karo
strings audio.wav | grep -i "flag\|secret"
```

> **Spectrogram mein kya dikhega?** Agar data hai toh frequency plot mein
> clearly text ya image nazar aayega — jaise "FLAG{audio}" ya koi shape.

---

## CASE 10: Multi-Layer Stego — Ek Ke Andar Ek

Kabhi kabhi multiple layers hoti hain. Patience rakho aur ek ek layer solve karo.

```bash
# Layer 1: Binwalk se extract karo
binwalk -e outer_image.jpg
ls _outer_image.jpg.extracted/
# Mila: inner_image.png

# Layer 2: zsteg se PNG check karo
zsteg _outer_image.jpg.extracted/inner_image.png
# Mila: Base64 encoded text

# Layer 3: Base64 decode karo
echo "RkxBR3toaWRkZW59" | base64 -d
# Output: FLAG{hidden}
```

**Multi-layer approach:**
1. Pehle binwalk se embedded files nikalo
2. Phir har extracted file pe appropriate tool chalao
3. Output ko decode karo (Base64, Hex, ROT13 etc.)
4. Tab tak repeat karo jab tak final answer na mile

---

## Common Mistakes (Mat Karna Ye)

1. **PNG pe steghide use karna** → "unsupported format" error aayega
   - PNG ke liye zsteg use karo, steghide sirf JPEG/WAV ke liye

2. **File type check nahi karna** → Galat tool use karoge
   - Hamesha `file` command se pehle actual type confirm karo
   - Extension pe bharosa mat karo (`.jpg` actually PNG ho sakta hai)

3. **Empty password try nahi karna** → Steghide mein pehle bina password try karo
   - Bahut baar password hota hi nahi — just Enter press karo

4. **Exiftool skip karna** → Easiest hiding spot miss ho jaata hai
   - 2 second lagta hai — hamesha run karo!

5. **Strings command bhool jaana** → Sabse quick check hai
   - `strings file | grep -i flag` — hamesha pehle ye karo

6. **Stegseek ke baare mein nahi pata** → Steghide password manually guess kar rahe ho
   - stegseek seconds mein password crack kar deta hai with rockyou.txt

7. **Extracted folder check nahi karna** → Binwalk ne extract kiya lekin dekha nahi
   - `_filename.extracted/` folder zaroor check karo

---

## Quick Decision Tree

```
Suspicious file mili — kya karu?
  │
  ├─ STEP 1: Basics (Hamesha karo!)
  │   ├─ file suspicious_file     → actual type kya hai?
  │   ├─ exiftool suspicious_file → metadata mein kuch hai?
  │   ├─ strings file | grep flag → quick text search
  │   └─ binwalk suspicious_file  → kuch embedded hai?
  │
  ├─ STEP 2: File type ke hisaab se
  │   ├─ JPEG hai?
  │   │   ├─ steghide extract -sf image.jpg (empty pass try karo)
  │   │   ├─ Password pata hai? → steghide -p "pass"
  │   │   ├─ Password nahi pata? → stegseek image.jpg rockyou.txt
  │   │   └─ EXIF Comment check karo (password hint?)
  │   │
  │   ├─ PNG hai?
  │   │   ├─ zsteg image.png (auto scan)
  │   │   ├─ zsteg -a image.png (thorough)
  │   │   └─ stegsolve (visual bit plane analysis)
  │   │
  │   ├─ Text file hai?
  │   │   └─ snow -C file.txt (whitespace stego)
  │   │
  │   ├─ Audio (WAV) hai?
  │   │   ├─ steghide extract -sf audio.wav
  │   │   └─ Spectrogram check karo (Audacity/Sonic Visualiser)
  │   │
  │   └─ Kuch bhi hai?
  │       └─ binwalk -e file → extracted folder check karo
  │
  └─ STEP 3: Kuch nahi mila?
      ├─ xxd file | tail -20 → EOF ke baad data?
      ├─ stegsolve → bit planes mein QR/text?
      └─ Multi-layer? → extract → phir se check karo
```
