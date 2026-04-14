# Q139 — peframe on Windows EXE

| Field | Value |
|---|---|
| **Target** | (local sample files) |
| **Domain** | 06 — Malware / Forensics |
| **Difficulty** | 🟡 Medium |
| **Tools** | `peframe` |
| **Time budget** | 10–15 min |

---

## 📝 Question

Run peframe against `sample.exe`. What headers does it parse?

---

## 🎯 Flag Format

```
headers=<text>
```

Example: `headers=PE`

**⚠️ Format strict hai** — lowercase, no spaces around `=`, semicolon separator where shown.

---

## 💡 Hints

<details>
<summary>Hint 1 (click)</summary>

`peframe sample.exe`
</details>

<details>
<summary>Hint 2</summary>

Outputs imports, sections, suspicious strings.
</details>

---

## ✅ Solution

<details>
<summary>Show solution</summary>

```bash
peframe sample.exe
```

Parses PE header, sections, and indicators.

**Answer:** `headers=PE`

📖 Ref: [playbooks/malware-playbook.md](../../playbooks/malware-playbook.md)
</details>

---

## 🤖 Claude Setup Prompt (for Claude-on-your-PC)

```
Pre-requisites for Q139:
1. Local Parrot exercise.
2. On Parrot install: pip3 install peframe (or sudo apt install peframe).
3. Stage sample.exe — any PE file, e.g. download calc.exe or putty.exe.

Report back: "Lab ready for Q139 — peframe installed, sample.exe staged".
```
