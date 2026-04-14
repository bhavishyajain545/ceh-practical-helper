# Q099 — John with Rules

| Field | Value |
|---|---|
| **Target** | `192.168.52.129` / `192.168.52.130` |
| **Domain** | 05 — Credential Attacks |
| **Difficulty** | 🔴 Hard |
| **Tools** | `john` |
| **Time budget** | 10–20 min |

---

## 📝 Question

Crack hash list with john using `--rules`. Provide command using rockyou wordlist.

---

## 🎯 Flag Format

```
cmd=<short>
```

Example: `cmd=john --wordlist=rockyou.txt --rules hashes.txt`

**⚠️ Format strict hai** — lowercase, no spaces around `=`, semicolon separator where shown.

---

## 💡 Hints

<details>
<summary>Hint 1 (click)</summary>

`--rules=Single` or `--rules=Wordlist`.
</details>

<details>
<summary>Hint 2</summary>

Mutates wordlist (toggle case, append numbers).
</details>

---

## ✅ Solution

<details>
<summary>Show solution</summary>

```bash
john --wordlist=/usr/share/wordlists/rockyou.txt --rules hashes.txt
```

Increases hit rate via mutations.

**Answer:** `cmd=john --wordlist=rockyou.txt --rules hashes.txt`

📖 Ref: [tools/john.md](../../tools/john.md)
</details>

---

## 🤖 Claude Setup Prompt (for Claude-on-your-PC)

```
Pre-requisites for Q099:
1. Local Parrot exercise — provide any hash file (hashes.txt) to crack.
2. rockyou.txt unzipped: ls /usr/share/wordlists/rockyou.txt.
3. john installed.

Report back: "Lab ready for Q099 — john + rockyou available on Parrot".
```
