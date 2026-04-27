# Q154 — Extract Hidden Data Using OpenStego

| Field | Value |
|-------|-------|
| **Target** | local |
| **Domain** | Steganography |
| **Difficulty** | 🟡 Medium |
| **Tools** | `openstego` |
| **Time budget** | 8–12 min |

---

## 📝 Question

"A PNG image `document.png` has data hidden using OpenStego. Extract the hidden message. The password is `opensteg0`. Report the flag found."

---

## 🎯 Flag Format

```
flag=<string>
```

Example: `flag=OPEN_STEGO_FLAG`

**⚠️ Format strict hai** — lowercase, no spaces around `=`, semicolon separator where shown.

---

## 💡 Hints

**Hint 1**

OpenStego GUI ya CLI dono se extract kar sakte ho.

**Hint 2**

CLI: `openstego extract --stegfile document.png --password opensteg0`

---

## ✅ Solution

```bash
openstego extract --stegfile document.png --password opensteg0
```
Or use OpenStego GUI: Open → Extract Data → Select file → Enter password.

**Answer:** `flag=OPENSTEG_DATA_FOUND`

---

## 🤖 Claude Setup Prompt

1. Create a PNG image with OpenStego embedded data:
```bash
convert -size 300x300 xc:yellow /tmp/document.png
echo "OPENSTEG_DATA_FOUND" > /tmp/hidden.txt
openstego embed --messagefile /tmp/hidden.txt --coverfile /tmp/document.png --stegfile /tmp/document_steg.png --password opensteg0
mv /tmp/document_steg.png document.png
```

Report back: "Lab ready for Q154 — document.png with OpenStego hidden data ready"
