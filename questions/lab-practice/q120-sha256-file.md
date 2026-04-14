# Q120 — SHA-256 of File

| Field | Value |
|---|---|
| **Target** | (local files on Parrot) |
| **Domain** | 18 — Cryptography |
| **Difficulty** | 🟢 Easy |
| **Tools** | `sha256sum` |
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
Pre-requisites for Q120:
1. Local Parrot exercise.
2. which sha256sum.

Report back: "Lab ready for Q120 — sha256sum available on Parrot".
```
