# Q114 — Binwalk Extract Embedded Files

| Field | Value |
|---|---|
| **Target** | (local files on Parrot) |
| **Domain** | 18 — Cryptography |
| **Difficulty** | 🟡 Medium |
| **Tools** | `openssl`, `steghide`, `binwalk`, `exiftool`, `hashid` |
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
1. Verify VMs running: ping 192.168.52.129 and ping 192.168.52.130 from 192.168.52.128.
2. Required services for this Q already up by default on the relevant target.
3. If something is down, restart it on the target VM.

Report back: "Lab ready for Q114".
```
