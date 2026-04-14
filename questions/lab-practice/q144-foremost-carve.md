# Q144 — Foremost File Carving

| Field | Value |
|---|---|
| **Target** | (local sample files) |
| **Domain** | 06 — Malware / Forensics |
| **Difficulty** | 🟡 Medium |
| **Tools** | `foremost` |
| **Time budget** | 10–15 min |

---

## 📝 Question

Use foremost to carve a disk image `image.dd`. Provide command.

---

## 🎯 Flag Format

```
cmd=<short>
```

Example: `cmd=foremost -i image.dd -o output/`

**⚠️ Format strict hai** — lowercase, no spaces around `=`, semicolon separator where shown.

---

## 💡 Hints

<details>
<summary>Hint 1 (click)</summary>

`-i` input, `-o` output dir.
</details>

<details>
<summary>Hint 2</summary>

Recovers JPG, PDF, etc.
</details>

---

## ✅ Solution

<details>
<summary>Show solution</summary>

```bash
foremost -i image.dd -o output/
```

Creates output/ with recovered files by type.

**Answer:** `cmd=foremost -i image.dd -o output/`

📖 Ref: [tools/foremost.md](../../tools/foremost.md)
</details>

---

## 🤖 Claude Setup Prompt (for Claude-on-your-PC)

```
Pre-requisites for Q144:
1. Local Parrot exercise.
2. which foremost (sudo apt install foremost).
3. Stage image.dd — create a tiny test: dd if=/dev/zero of=image.dd bs=1M count=10; cat /usr/share/pixmaps/*.png >> image.dd (foremost will carve the PNGs).

Report back: "Lab ready for Q144 — foremost installed, image.dd with embedded files staged".
```
