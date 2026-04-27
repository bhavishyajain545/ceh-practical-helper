# Q157 — Hide Message in Text File Using Snow

| Field | Value |
|-------|-------|
| **Target** | local |
| **Domain** | Steganography |
| **Difficulty** | 🟢 Easy |
| **Tools** | `snow` |
| **Time budget** | 5–8 min |

---

## 📝 Question

"Take the file `cover.txt` and hide the message `CEH_PASS_2024` using Snow with password `snowfall`. Verify the message was embedded by extracting it back."

---

## 🎯 Flag Format

```
hidden=<yes|no>; message=<string>
```

Example: `hidden=yes; message=CEH_PASS_2024`

**⚠️ Format strict hai** — lowercase, no spaces around `=`, semicolon separator where shown.

---

## 💡 Hints

**Hint 1**

`snow -C -m "message" -p password infile outfile`

**Hint 2**

Verify: `snow -C -p snowfall outfile.txt`

---

## ✅ Solution

```bash
snow -C -m "CEH_PASS_2024" -p snowfall cover.txt steg_cover.txt
snow -C -p snowfall steg_cover.txt
```

**Answer:** `hidden=yes; message=CEH_PASS_2024`

---

## 🤖 Claude Setup Prompt

1. Create cover text file:
```bash
echo -e "This is a sample document.\nIt contains multiple lines.\nNothing suspicious here." > cover.txt
```

Report back: "Lab ready for Q157 — cover.txt ready for Snow embedding"
