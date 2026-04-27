# Q170 — Analyze Image Bit Planes Using Stegsolve

| Field | Value |
|-------|-------|
| **Target** | local |
| **Domain** | Steganography |
| **Difficulty** | 🟡 Medium |
| **Tools** | `stegsolve` |
| **Time budget** | 8–12 min |

---

## 📝 Question

"An image `bitplane.png` has a flag hidden in a specific color bit plane. Use Stegsolve to cycle through bit planes (Red plane 0, Green plane 0, Blue plane 0, etc.) and find which plane reveals the hidden QR code or text. Report which plane contains the flag."

---

## 🎯 Flag Format

```
plane=<color_bit>; flag=<string>
```

Example: `plane=red_0; flag=BITPLANE_FLAG`

**⚠️ Format strict hai** — lowercase, no spaces around `=`, semicolon separator where shown.

---

## 💡 Hints

**Hint 1**

Stegsolve GUI mein arrow keys se bit planes cycle karo — kisi ek mein hidden pattern dikhega.

**Hint 2**

Usually Red 0 ya Blue 0 plane mein data hota hai — LSB plane.

---

## ✅ Solution

1. Open Stegsolve: `java -jar stegsolve.jar`
2. File → Open → `bitplane.png`
3. Use arrow keys to cycle through planes
4. Red plane 0 reveals hidden text/QR

**Answer:** `plane=red_0; flag=CEH_BITPLANE_2024`

---

## 🤖 Claude Setup Prompt

1. Create PNG with data hidden in Red bit plane 0:
```python
from PIL import Image, ImageDraw, ImageFont
# Create base image
img = Image.new('RGB', (300,300), (128,128,128))
# Encode "CEH_BITPLANE_2024" in Red LSB
pixels = img.load()
# Write flag text as pattern in Red plane 0
flag_img = Image.new('1', (300,300), 0)
draw = ImageDraw.Draw(flag_img)
draw.text((50,130), "CEH_BITPLANE_2024", fill=1)
for x in range(300):
    for y in range(300):
        r,g,b = pixels[x,y]
        bit = flag_img.getpixel((x,y))
        pixels[x,y] = ((r & ~1) | bit, g, b)
img.save('bitplane.png')
```

Report back: "Lab ready for Q170 — bitplane.png with hidden bit plane data ready"
