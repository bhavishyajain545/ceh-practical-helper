# Q114 — Binwalk Extract Embedded Files

| Field | Value |
|---|---|
| **Target** | (local files on Parrot) |
| **Domain** | 18 — Cryptography |
| **Difficulty** | 🟡 Medium |
| **Tools** | `binwalk` |
| **Time budget** | 10–15 min |

---

## 📝 Question

Run binwalk on `firmware.bin` to extract embedded filesystems. Provide command.

---

## 🎯 Flag Format

```
cmd=<short>
```

Example: `cmd=binwalk -e firmware.bin`

**⚠️ Format strict hai** — lowercase, no spaces around `=`, semicolon separator where shown.

---

## 💡 Hints

<details>
<summary>Hint 1 (click)</summary>

`-e` extracts.
</details>

<details>
<summary>Hint 2</summary>

`-Me` for recursive.
</details>

---

## ✅ Solution

<details>
<summary>Show solution</summary>

```bash
binwalk -e firmware.bin
```

Creates `_firmware.bin.extracted/` with embedded contents.

**Answer:** `cmd=binwalk -e firmware.bin`

📖 Ref: [tools/binwalk.md](../../tools/binwalk.md)
</details>

---

## 🤖 Claude Setup Prompt (for Claude-on-your-PC)

```
Pre-requisites for Q114:
1. Local Parrot exercise.
2. which binwalk (sudo apt install binwalk).
3. Stage firmware.bin sample — e.g. combine: cat /bin/ls /etc/passwd > firmware.bin; or download a test firmware.

Report back: "Lab ready for Q114 — binwalk installed, firmware.bin staged".
```
