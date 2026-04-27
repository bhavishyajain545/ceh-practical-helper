# Q162 — Identify File Type by Magic Bytes

| Field | Value |
|-------|-------|
| **Target** | local |
| **Domain** | Steganography |
| **Difficulty** | 🟢 Easy |
| **Tools** | `file, xxd` |
| **Time budget** | 3–5 min |

---

## 📝 Question

"A file `unknown_file.dat` has its extension removed. Identify the actual file type by examining its magic bytes/file header. Report the real file type and the first 4 bytes in hex."

---

## 🎯 Flag Format

```
filetype=<type>; magic=<hex_bytes>
```

Example: `filetype=png; magic=89504E47`

**⚠️ Format strict hai** — lowercase, no spaces around `=`, semicolon separator where shown.

---

## 💡 Hints

**Hint 1**

`file unknown_file.dat` — ye magic bytes se file type identify karega.

**Hint 2**

`xxd unknown_file.dat | head -1` se hex dump dekho — pehle bytes file signature hain.

---

## ✅ Solution

```bash
file unknown_file.dat
xxd unknown_file.dat | head -1
```

**Answer:** `filetype=pdf; magic=25504446`

---

## 🤖 Claude Setup Prompt

1. Create a renamed PDF file:
```bash
echo "%PDF-1.4 fake pdf content with flag CEH_MAGIC_BYTES" > /tmp/unknown_file.dat
cp /tmp/unknown_file.dat unknown_file.dat
```

Report back: "Lab ready for Q162 — unknown_file.dat with hidden file type ready"
