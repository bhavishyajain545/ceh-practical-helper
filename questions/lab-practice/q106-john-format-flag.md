# Q106 — John Format Auto-Detection Failure

| Field | Value |
|---|---|
| **Target** | `192.168.52.129` / `192.168.52.130` |
| **Domain** | 05 — Credential Attacks |
| **Difficulty** | 🟡 Medium |
| **Tools** | `john` |
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
Pre-requisites for Q106:
1. Local Parrot exercise.
2. which john — ensure sample hashes.txt file is present in cwd before running.

Report back: "Lab ready for Q106 — john installed on Parrot".
```
