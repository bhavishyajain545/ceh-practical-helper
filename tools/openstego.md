# OpenStego — GUI Steganography & Watermarking

> "GUI-based stego tool for hiding data in images. Exam mein agar question says 'OpenStego was used' — toh OpenStego se hi extract karna padega."

**Install check (Parrot):** `openstego --help` or launch from Applications menu

---

## 🎯 Cheat-flow

| You need to... | Command / Action |
|---|---|
| Extract hidden data (CLI) | `openstego extract --stegfile steg.png --password pass123` |
| Embed data (CLI) | `openstego embed --messagefile secret.txt --coverfile cover.png --stegfile out.png` |
| Extract (GUI) | Open OpenStego → Extract Data → Select file → Enter password |
| Embed (GUI) | Open OpenStego → Hide Data → Select files → Set password |
| Extract watermark | `openstego extractwatermark --stegfile img.png --origfile orig.png` |

---

## 🔑 CLI Flags

| Flag | Meaning |
|---|---|
| `--messagefile` | File to hide |
| `--coverfile` | Cover image |
| `--stegfile` | Output stego image / input for extraction |
| `--password` | Password for encryption |
| `--algorithm` | Stego algorithm (default: RandomLSB) |
| `--extractdir` | Directory to extract to |

---

## 📋 Recipes

```bash
# 1. Extract hidden data
openstego extract --stegfile suspicious.png --password "secret"

# 2. Extract without password
openstego extract --stegfile suspicious.png

# 3. Embed data into image
openstego embed --messagefile flag.txt --coverfile photo.png --stegfile steg_photo.png --password "mypass"

# 4. Extract watermark (needs original)
openstego extractwatermark --stegfile watermarked.png --origfile original.png

# 5. Embed watermark
openstego embedwatermark --signaturefile sig.txt --coverfile photo.png --stegfile wm_photo.png
```

---

## 💡 Exam Tips

- OpenStego works with **PNG images** — not JPEG
- Two modes: **Data Hiding** and **Digital Watermarking**
- If GUI available, use it — faster than remembering CLI flags
- Data hiding uses RandomLSB algorithm by default
- Watermark extraction needs the **original image** for comparison
- Password can be empty — try without password first

---

## ⚠️ Gotchas

- **JPEG not supported** — only PNG and BMP
- CLI may not be in PATH — try `java -jar OpenStego.jar` instead
- Watermark extraction is DIFFERENT from data extraction
- OpenStego-embedded data can ONLY be extracted by OpenStego (not steghide/zsteg)

---

## 🔗 Related

- [steghide.md](steghide.md) — JPEG stego
- [zsteg.md](zsteg.md) — PNG LSB detection (different method)
