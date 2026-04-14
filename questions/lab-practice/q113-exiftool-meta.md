# Q113 — Exiftool Metadata

| Field | Value |
|---|---|
| **Target** | (local files on Parrot) |
| **Domain** | 18 — Cryptography |
| **Difficulty** | 🟢 Easy |
| **Tools** | `openssl`, `steghide`, `binwalk`, `exiftool`, `hashid` |
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
1. Verify VMs running: ping 192.168.52.129 and ping 192.168.52.130 from 192.168.52.128.
2. Required services for this Q already up by default on the relevant target.
3. If something is down, restart it on the target VM.

Report back: "Lab ready for Q113".
```
