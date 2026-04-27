# Q166 — Compare Two Images to Find Hidden Differences

| Field | Value |
|-------|-------|
| **Target** | local |
| **Domain** | Steganography |
| **Difficulty** | 🟡 Medium |
| **Tools** | `compare (ImageMagick), diff` |
| **Time budget** | 5–10 min |

---

## 📝 Question

"Two images `original.png` and `modified.png` look identical to the naked eye but one has been altered to hide data. Use image comparison tools to find the differences and identify what was changed. Report the number of different pixels."

---

## 🎯 Flag Format

```
diff_pixels=<number>; flag=<string>
```

Example: `diff_pixels=150; flag=PIXEL_DIFF`

**⚠️ Format strict hai** — lowercase, no spaces around `=`, semicolon separator where shown.

---

## 💡 Hints

**Hint 1**

`compare original.png modified.png diff.png` — differences highlight ho jayengi.

**Hint 2**

`compare -metric AE original.png modified.png null:` — ye exact different pixel count dega.

---

## ✅ Solution

```bash
compare -metric AE original.png modified.png null: 2>&1
compare original.png modified.png -highlight-color red diff.png
```

**Answer:** `diff_pixels=42; flag=VISUAL_DIFF_FOUND`

---

## 🤖 Claude Setup Prompt

1. Create two images with subtle differences:
```bash
convert -size 200x200 xc:white /tmp/original.png
cp /tmp/original.png /tmp/modified.png
# Modify specific pixels to encode flag
convert /tmp/modified.png -fill "rgb(255,254,255)" -draw "point 10,10" -draw "point 11,10" /tmp/modified.png
```

Report back: "Lab ready for Q166 — original.png and modified.png with hidden differences ready"
