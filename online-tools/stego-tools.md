# 🖼 Steganography Tools (Online)

> Image / audio / file ke andar hidden data nikalne ke liye.

## Image stego

| Tool | URL | Kya karta hai | Kab use karu |
|---|---|---|---|
| **Aperi'Solve** | https://aperisolve.fr/ | **All-in-one** — automatically runs zsteg, steghide, stegsolve, exiftool, binwalk on uploaded image | **Pehla tool to try.** Image upload → it runs everything → reports findings |
| **StegOnline** | https://stegonline.georgeom.net/ | Bit-plane visualization, LSB extract, channel separation | Manual visual analysis |
| **OnlineStegCrack** | https://stegcracker.com/ (or similar) | Steghide brute force online | If steghide tool fail ho |
| **FotoForensics** | https://fotoforensics.com/ | ELA (Error Level Analysis), JPEG quality, metadata | Visual hidden content detection |
| **EXIF Online** | https://onlineexifviewer.com/ | Quick metadata view | If exiftool not installed |

### Aperi'Solve workflow (best for stego CEH questions)

1. https://aperisolve.fr/ open karo
2. Image upload karo
3. Wait 10-30 seconds
4. Output mein dekho:
   - **Strings** — visible text/credentials
   - **Exif** — author, GPS, comment fields
   - **Steghide** — brute force results
   - **Zsteg** — LSB analysis (PNG/BMP)
   - **Binwalk** — embedded files
   - **StegSolve** — visual channel views

**Hinglish tip:** Agar local mein steghide se "wrong passphrase" aaye, aperisolve try karo — wo common passwords brute karta hai automatically.

---

## Audio stego

| Tool | URL | Use |
|---|---|---|
| **Sonic Visualiser** | https://www.sonicvisualiser.org/ (download) | Spectrogram view — hidden text in audio dikhta hai visually |
| **DeepSound** (download) | http://jpinsoft.net/deepsound/ | Audio stego encode/decode |
| **Online Spectrogram** | https://academo.org/demos/spectrum-analyzer/ | Quick spectrogram in browser |

**Common CEH audio stego pattern:** WAV file → spectrogram view → hidden text/QR code visible in frequency domain.

---

## File metadata online

| Tool | URL | Use |
|---|---|---|
| **Get-Metadata.com** | https://www.get-metadata.com/ | Universal metadata extractor (PDF, DOC, image, etc.) |
| **Metadata Remover** | https://metadataremover.ai/metadata-viewer | Browser-local image metadata viewer/remover (JPG, PNG, WebP); no account |
| **OnlineEXIF** | https://onlineexifviewer.com/ | Image EXIF only |
| **MetaPicz** | http://metapicz.com/ | Photo metadata + GPS map view |

---

## Hex viewer (for binary file analysis)

| Tool | URL | Use |
|---|---|---|
| **HexEd.it** | https://hexed.it/ | Browser-based hex editor — open file, look for headers/strings |
| **Online Hex Viewer** | https://hexviewer.com/ | Read-only viewer |

---

## Quick stego decision tree

```
File diya hai → kya type hai?
│
├── Image (PNG/JPG/BMP)
│   ├── Pehle: Aperi'Solve
│   ├── Manual: exiftool → strings → binwalk → steghide
│   └── Visual: StegOnline / FotoForensics
│
├── Audio (WAV/MP3)
│   ├── Sonic Visualiser → spectrogram
│   └── strings on file (sometimes plaintext hai)
│
├── PDF / Office doc
│   ├── Get-Metadata.com
│   ├── exiftool / olevba (macros)
│   └── strings + binwalk
│
└── Unknown binary
    ├── HexEd.it
    ├── strings <file>
    └── binwalk -e <file>
```

---

## ⚠️ Gotchas

- **File size limits:** most online stego tools cap at 5-20MB
- **Upload privacy:** for CEH lab files this is fine, real-world mein never upload sensitive files
- **Aperi'Solve sometimes slow:** if it hangs, refresh and re-upload
- **Steghide passwords:** try blank password first (`steghide extract -sf file.jpg` then just press Enter)
