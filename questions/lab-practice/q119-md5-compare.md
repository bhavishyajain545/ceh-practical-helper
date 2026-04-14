# Q119 — MD5 Integrity Check

| Field | Value |
|---|---|
| **Target** | (local files on Parrot) |
| **Domain** | 18 — Cryptography |
| **Difficulty** | 🟢 Easy |
| **Tools** | `openssl`, `steghide`, `binwalk`, `exiftool`, `hashid` |
| **Time budget** | 10–15 min |

---

## 📝 Question

Compute MD5 of `hello` and compare to `5d41402abc4b2a76b9719d911017c592`.

---

## 🎯 Flag Format

```
match=<yes|no>
```

Example: `match=yes`

**⚠️ Format strict hai** — lowercase, no spaces around `=`, semicolon separator where shown.

---

## 💡 Hints

<details>
<summary>Hint 1 (click)</summary>

`echo -n 'hello' | md5sum`
</details>

<details>
<summary>Hint 2</summary>

Note `-n` to avoid newline.
</details>

---

## ✅ Solution

<details>
<summary>Show solution</summary>

```bash
echo -n 'hello' | md5sum
```

Outputs `5d41402abc4b2a76b9719d911017c592` — match.

**Answer:** `match=yes`

📖 Ref: [tools/md5sum.md](../../tools/md5sum.md)
</details>

---

## 🤖 Claude Setup Prompt (for Claude-on-your-PC)

```
1. Verify VMs running: ping 192.168.52.129 and ping 192.168.52.130 from 192.168.52.128.
2. Required services for this Q already up by default on the relevant target.
3. If something is down, restart it on the target VM.

Report back: "Lab ready for Q119".
```
