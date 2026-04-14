# Q106 — John Format Auto-Detection Failure

| Field | Value |
|---|---|
| **Target** | `192.168.52.129` / `192.168.52.130` |
| **Domain** | 05 — Credential Attacks |
| **Difficulty** | 🟡 Medium |
| **Tools** | `hydra`, `john`, `hashcat`, `crackmapexec`, `responder` |
| **Time budget** | 10–20 min |

---

## 📝 Question

You have a hash and john says `No password hashes loaded`. What flag forces format?

---

## 🎯 Flag Format

```
flag=<name>
```

Example: `flag=--format=<name>`

**⚠️ Format strict hai** — lowercase, no spaces around `=`, semicolon separator where shown.

---

## 💡 Hints

<details>
<summary>Hint 1 (click)</summary>

`john --list=formats` shows options.
</details>

<details>
<summary>Hint 2</summary>

E.g., `--format=NT`.
</details>

---

## ✅ Solution

<details>
<summary>Show solution</summary>

```bash
john --format=NT hashes.txt
```

Forces NT format.

**Answer:** `flag=--format=NT`

📖 Ref: [tools/john.md](../../tools/john.md)
</details>

---

## 🤖 Claude Setup Prompt (for Claude-on-your-PC)

```
1. Verify VMs running: ping 192.168.52.129 and ping 192.168.52.130 from 192.168.52.128.
2. Required services for this Q already up by default on the relevant target.
3. If something is down, restart it on the target VM.

Report back: "Lab ready for Q106".
```
