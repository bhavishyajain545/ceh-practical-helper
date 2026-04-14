# Q120 — SHA-256 of File

| Field | Value |
|---|---|
| **Target** | (local files on Parrot) |
| **Domain** | 18 — Cryptography |
| **Difficulty** | 🟢 Easy |
| **Tools** | `openssl`, `steghide`, `binwalk`, `exiftool`, `hashid` |
| **Time budget** | 10–15 min |

---

## 📝 Question

Compute SHA-256 of `/etc/hostname` on Parrot.

---

## 🎯 Flag Format

```
cmd=<short>
```

Example: `cmd=sha256sum /etc/hostname`

**⚠️ Format strict hai** — lowercase, no spaces around `=`, semicolon separator where shown.

---

## 💡 Hints

<details>
<summary>Hint 1 (click)</summary>

`sha256sum <file>`
</details>

<details>
<summary>Hint 2</summary>

Output hex digest + filename.
</details>

---

## ✅ Solution

<details>
<summary>Show solution</summary>

```bash
sha256sum /etc/hostname
```

Returns 64-char hex hash.

**Answer:** `cmd=sha256sum /etc/hostname`

📖 Ref: [tools/md5sum.md](../../tools/md5sum.md)
</details>

---

## 🤖 Claude Setup Prompt (for Claude-on-your-PC)

```
1. Verify VMs running: ping 192.168.52.129 and ping 192.168.52.130 from 192.168.52.128.
2. Required services for this Q already up by default on the relevant target.
3. If something is down, restart it on the target VM.

Report back: "Lab ready for Q120".
```
