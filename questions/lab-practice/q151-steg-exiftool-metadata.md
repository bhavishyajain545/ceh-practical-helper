# Q151 — Extract Hidden Info from EXIF Metadata

| Field | Value |
|-------|-------|
| **Target** | local |
| **Domain** | Steganography |
| **Difficulty** | 🟢 Easy |
| **Tools** | `exiftool` |
| **Time budget** | 5–8 min |

---

## 📝 Question

"A suspicious image file `secret_photo.jpg` has been found on the target. Use exiftool to examine its metadata and find the hidden flag embedded in the EXIF Comment field."

---

## 🎯 Flag Format

```
flag=<string>
```

Example: `flag=CEH_METADATA_FOUND`

**⚠️ Format strict hai** — lowercase, no spaces around `=`, semicolon separator where shown.

---

## 💡 Hints

**Hint 1**

`exiftool` se saare metadata fields dekho — Comment field pe dhyan do.

**Hint 2**

`exiftool -Comment secret_photo.jpg` se directly Comment field extract hoga.

---

## ✅ Solution

```bash
exiftool secret_photo.jpg
```

Ya specifically:
```bash
exiftool -Comment secret_photo.jpg
```

**Answer:** `flag=CEH_STEG_EXIF_2024`

---

## 🤖 Claude Setup Prompt

1. Create a JPEG image with hidden EXIF comment:
```bash
convert -size 200x200 xc:blue /tmp/secret_photo.jpg
exiftool -Comment="CEH_STEG_EXIF_2024" /tmp/secret_photo.jpg
```
2. Place file on Desktop or working directory.

Report back: "Lab ready for Q151 — secret_photo.jpg with hidden EXIF comment placed"
