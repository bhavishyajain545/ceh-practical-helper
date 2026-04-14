# Q144 — Foremost File Carving

| Field | Value |
|---|---|
| **Target** | (local sample files) |
| **Domain** | 06 — Malware / Forensics |
| **Difficulty** | 🟡 Medium |
| **Tools** | `strings`, `file`, `peframe`, `olevba`, `yara`, `foremost` |
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
1. Verify VMs running: ping 192.168.52.129 and ping 192.168.52.130 from 192.168.52.128.
2. Required services for this Q already up by default on the relevant target.
3. If something is down, restart it on the target VM.

Report back: "Lab ready for Q144".
```
