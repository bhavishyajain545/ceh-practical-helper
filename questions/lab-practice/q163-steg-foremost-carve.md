# Q163 — Carve Hidden Files from Disk Image Using Foremost

| Field | Value |
|-------|-------|
| **Target** | local |
| **Domain** | Steganography |
| **Difficulty** | 🟡 Medium |
| **Tools** | `foremost` |
| **Time budget** | 8–12 min |

---

## 📝 Question

"A disk image `evidence.dd` contains deleted files. Use foremost to carve and recover files from the image. Report how many JPEG files were recovered and the flag found in any recovered text file."

---

## 🎯 Flag Format

```
jpeg_count=<number>; flag=<string>
```

Example: `jpeg_count=3; flag=CARVED_DATA`

**⚠️ Format strict hai** — lowercase, no spaces around `=`, semicolon separator where shown.

---

## 💡 Hints

**Hint 1**

`foremost -i evidence.dd -o output_dir`

**Hint 2**

Recovered files `output_dir/` ke sub-folders mein type-wise milenge (jpg/, txt/, etc.)

---

## ✅ Solution

```bash
foremost -i evidence.dd -o carved_output
ls carved_output/jpg/ | wc -l
cat carved_output/txt/*.txt
```

**Answer:** `jpeg_count=2; flag=FOREMOST_RECOVERY`

---

## 🤖 Claude Setup Prompt

1. Create a disk image with embedded files:
```bash
dd if=/dev/zero of=/tmp/evidence.dd bs=1M count=5
# Embed files at various offsets
convert -size 100x100 xc:red /tmp/img1.jpg
convert -size 100x100 xc:blue /tmp/img2.jpg
echo "FOREMOST_RECOVERY" > /tmp/flag.txt
dd if=/tmp/img1.jpg of=/tmp/evidence.dd bs=1 seek=1024 conv=notrunc
dd if=/tmp/img2.jpg of=/tmp/evidence.dd bs=1 seek=102400 conv=notrunc
dd if=/tmp/flag.txt of=/tmp/evidence.dd bs=1 seek=204800 conv=notrunc
cp /tmp/evidence.dd evidence.dd
```

Report back: "Lab ready for Q163 — evidence.dd with carved files ready"
