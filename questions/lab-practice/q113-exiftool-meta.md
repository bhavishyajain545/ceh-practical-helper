# Q113 — Exiftool Metadata

| Field | Value |
|---|---|
| **Target** | (local files on Parrot) |
| **Domain** | 18 — Cryptography |
| **Difficulty** | 🟢 Easy |
| **Tools** | `exiftool` |
| **Time budget** | 10–15 min |

---

## 📝 Question

Run `exiftool` on a JPEG and identify the **GPS coordinate** field name.

---

## 🎯 Flag Format

```
field=<name>
```

Example: `field=GPSPosition`

**⚠️ Format strict hai** — lowercase, no spaces around `=`, semicolon separator where shown.

---

## 💡 Hints

<details>
<summary>Hint 1 (click)</summary>

`exiftool image.jpg | grep -i gps`
</details>

<details>
<summary>Hint 2</summary>

GPSPosition or GPSLatitude/GPSLongitude.
</details>

---

## ✅ Solution

<details>
<summary>Show solution</summary>

```bash
exiftool image.jpg
```

Reports GPSPosition if present.

**Answer:** `field=GPSPosition`

📖 Ref: [tools/exiftool.md](../../tools/exiftool.md)
</details>

---

## 🤖 Claude Setup Prompt (for Claude-on-your-PC)

```
Pre-requisites for Q113:
1. Local Parrot exercise.
2. which exiftool (sudo apt install exiftool/libimage-exiftool-perl).
3. Stage a JPEG with GPS data, e.g. download a sample: curl -o image.jpg https://exiftool.org/gps.jpg (or any JPEG with GPSPosition metadata).

Report back: "Lab ready for Q113 — exiftool installed, sample JPEG with GPSPosition in cwd".
```
