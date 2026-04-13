# exiftool — metadata reader/writer

> **Always run this first on any suspicious file.** Exam questions often hide passwords, GPS coordinates, author names, or steghide passphrases directly in the image's metadata. It's a 5-second check that frequently answers the whole question.

**Install check (Parrot — already installed):** `exiftool -ver`
**Install if missing:** `sudo apt install libimage-exiftool-perl`

---

## 🎯 Cheat-flow: "What's in this file's metadata?"

| You need... | Run this |
|---|---|
| **All metadata** (do this first) | `exiftool <file>` |
| Specific tag | `exiftool -GPSPosition <file>` |
| GPS coords as decimal | `exiftool -c "%.6f" -GPSPosition <file>` |
| Comment field only | `exiftool -Comment <file>` |
| Just author / creator | `exiftool -Author -Artist -Creator <file>` |
| Creation date | `exiftool -CreateDate -DateTimeOriginal <file>` |
| Camera make/model | `exiftool -Make -Model <file>` |
| Software that made it | `exiftool -Software <file>` |
| Batch a directory | `exiftool -r <dir>` |
| Write a tag | `exiftool -Comment="secret" <file>` |
| **Strip all metadata** | `exiftool -all= <file>` |
| Copy metadata from A to B | `exiftool -TagsFromFile A.jpg B.jpg` |

---

## 🔑 Flags you must know cold

### Reading
| Flag | Meaning |
|---|---|
| `<file>` | No flag = dump all tags |
| `-<Tag>` | Print only that tag |
| `-a` | Allow duplicate tags |
| `-G` | Show group name (e.g. `[EXIF]`) |
| `-s` | Short tag names (for scripting) |
| `-S` | Very short — one tag/value per line |
| `-n` | Numeric output (no unit strings) |
| `-c "%.6f"` | Coordinate format |
| `-b` | Binary output (useful for embedded thumbnails) |
| `-csv` | CSV output |
| `-json` | JSON output |
| `-r` | Recursive into dirs |
| `-ext jpg` | Only process this extension |
| `-if <cond>` | Conditional (e.g. `-if '$Make eq "Canon"'`) |

### Writing
| Flag | Meaning |
|---|---|
| `-Tag=value` | Set tag |
| `-Tag-=value` | Remove value |
| `-Tag+=value` | Append |
| `-all=` | **Strip ALL metadata** |
| `-overwrite_original` | Don't leave `file.jpg_original` backup |
| `-TagsFromFile src` | Copy tags from another file |
| `-P` | Preserve file modification time |

---

## 🧪 Tags that come up on the exam

| Tag | Where you might find secrets |
|---|---|
| `Comment` / `UserComment` | **Passphrases, hints, flags** |
| `Artist` / `Author` / `Creator` / `By-line` | Usernames |
| `Copyright` | Often re-used as passphrase |
| `Keywords` | Random hints |
| `Subject` / `Title` / `Description` | Direct text data |
| `GPSPosition` / `GPSLatitude` / `GPSLongitude` | **Location Qs** |
| `Make` / `Model` | Device type |
| `Software` | Tool that created/edited it |
| `CreateDate` / `DateTimeOriginal` / `ModifyDate` | Timeline Qs |
| `Producer` / `Creator` (PDFs) | PDF tool used |
| `XMP-*` groups | Adobe metadata — often overlooked |
| `ICC_Profile:*` | Rarely but sometimes |

---

## 📋 Command recipes (copy-paste)

### The "always run first" dump
```bash
exiftool suspicious.jpg

# With group prefixes (helps see EXIF vs XMP vs IPTC)
exiftool -G suspicious.jpg

# Short form for scripting / grep
exiftool -s -S suspicious.jpg
```

### GPS coordinates question
```bash
# Human-readable
exiftool -GPSPosition suspicious.jpg

# Decimal degrees (paste into Google Maps)
exiftool -c "%.6f" -GPSPosition suspicious.jpg

# Lat + Lon separately
exiftool -GPSLatitude -GPSLongitude -c "%.6f" suspicious.jpg

# Everything GPS
exiftool -gps:all suspicious.jpg
```

### Find a password / hint stored in metadata
```bash
# Dump everything then scan visually
exiftool suspicious.jpg | less

# Target likely fields
exiftool -Comment -UserComment -Artist -Author -Creator \
         -Copyright -Keywords -Subject -Title -Description \
         suspicious.jpg
```

### Extract the embedded JPEG thumbnail (sometimes contains the original uncensored image)
```bash
exiftool -b -ThumbnailImage suspicious.jpg > thumb.jpg
exiftool -b -PreviewImage    suspicious.jpg > preview.jpg
```

### Write metadata (for reverse / crafting Qs)
```bash
# Set a comment
exiftool -Comment="secret-passphrase" photo.jpg

# Set author + copyright
exiftool -Author="Alice" -Copyright="(c) 2026 Alice" photo.jpg

# Set GPS
exiftool -GPSLatitude=48.8584 -GPSLatitudeRef=N \
         -GPSLongitude=2.2945 -GPSLongitudeRef=E photo.jpg

# Don't keep the .jpg_original backup
exiftool -Comment="x" -overwrite_original photo.jpg
```

### Strip metadata (anti-forensics)
```bash
# Remove ALL metadata
exiftool -all= photo.jpg

# Remove only GPS
exiftool -gps:all= photo.jpg

# Remove EXIF but keep XMP
exiftool -EXIF:all= photo.jpg

# In place, no backup
exiftool -all= -overwrite_original photo.jpg
```

### Copy metadata between files
```bash
# Copy everything from a.jpg to b.jpg
exiftool -TagsFromFile a.jpg b.jpg

# Copy only GPS
exiftool -TagsFromFile a.jpg -gps:all b.jpg
```

### Batch a directory
```bash
exiftool -r -Comment -Author /path/to/dir
exiftool -r -ext jpg -all= /path/to/dir     # strip all JPGs recursively
```

---

## ⚠️ Gotchas

- **Runs on almost any file format** — JPEG, PNG, PDF, DOCX, MP3, MP4, HEIC. Always try it before assuming a file has no metadata.
- **`Comment` and `UserComment` are different tags.** Always check both.
- **GPS coordinates use ref fields** (`GPSLatitudeRef=N/S`, `GPSLongitudeRef=E/W`). Ignoring them flips hemispheres.
- **Writing leaves `.jpg_original` backup.** Use `-overwrite_original` if the Q expects a single file.
- **PDFs:** Check `Producer`, `Creator`, `Author`, `Title`, `Keywords`, and XMP fields.
- **Office docs (.docx, .xlsx):** exiftool reads them too — `LastModifiedBy`, `Creator`, `Company` are all there.
- **Answer format:** Copy the value *exactly* as printed, including any spaces or case. If Q asks for GPS, check if it wants DMS (`48 deg 51' 30.24"`) or decimal (`48.858400`).
- **Don't forget to run `strings` and `binwalk` too** — some data isn't in metadata but appended to the file.

---

## 🔗 Related

- [steghide — extract hidden payloads from JPEG/WAV](./steghide.md)
- [binwalk — find appended/embedded files](./binwalk.md)
- [foremost — carve files](./foremost.md)
- [Forensics question bank](../questions/by-domain/07-sniffing.md)
