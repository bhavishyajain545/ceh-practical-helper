# Q099 — John with Rules

| Field | Value |
|---|---|
| **Target** | `192.168.52.129` / `192.168.52.130` |
| **Domain** | 05 — Credential Attacks |
| **Difficulty** | 🔴 Hard |
| **Tools** | `hydra`, `john`, `hashcat`, `crackmapexec`, `responder` |
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
1. Verify VMs running: ping 192.168.52.129 and ping 192.168.52.130 from 192.168.52.128.
2. Required services for this Q already up by default on the relevant target.
3. If something is down, restart it on the target VM.

Report back: "Lab ready for Q099".
```
