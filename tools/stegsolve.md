# stegsolve — Image Bit Plane Analyzer

> "Java GUI tool for visual bit plane analysis. When zsteg/steghide dont find anything, stegsolve lets you SEE hidden patterns in image layers."

**Install/Run:** `java -jar stegsolve.jar` (download from GitHub)

---

## 🎯 How to Use

### Launch
```bash
java -jar /opt/stegsolve/stegsolve.jar
```

### Main Features

| Feature | How | What it does |
|---|---|---|
| **Bit Plane Browse** | Open image → Arrow keys | Cycle through R/G/B bit planes 0-7 |
| **Data Extract** | Analyse → Data Extract | Extract LSB data from selected channels |
| **Frame Browse** | Analyse → Frame Browser | Animated GIF frame analysis |
| **Image Combiner** | Analyse → Image Combiner | XOR/AND/OR two images |
| **Stereogram Solver** | Analyse → Stereogram | Solve stereogram images |

---

## 📋 Workflow

```
1. File → Open → select image
2. Use < > arrow buttons to cycle bit planes:
   - Red plane 0, Red plane 1, ... Red plane 7
   - Green plane 0, Green plane 1, ... Green plane 7
   - Blue plane 0, Blue plane 1, ... Blue plane 7
   - Alpha plane 0-7
   - Full red, Full green, Full blue
   - Random colour map, Gray bits
3. Look for hidden text, QR codes, or patterns in any plane
4. If found → note which plane has the data
5. Analyse → Data Extract → select that channel → extract
```

---

## 💡 Exam Tips

- **Plane 0** (LSB) is where data is usually hidden
- Look for **QR codes, text, or binary patterns** appearing in a specific plane
- If image looks normal but file is suspiciously large → try stegsolve
- **Image Combiner** useful when given two "identical" images — XOR reveals differences
- No CLI mode — GUI only, needs Java and display

---

## ⚠️ Gotchas

- Requires **Java** and **GUI access** (X11/display)
- No command-line mode available
- Works on PNG/BMP/GIF — JPEG loses LSB data due to compression
- Download manually — not in most package managers

---

## 🔗 Related

- [zsteg.md](zsteg.md) — automated LSB detection (CLI)
- [exiftool.md](exiftool.md) — metadata check first
