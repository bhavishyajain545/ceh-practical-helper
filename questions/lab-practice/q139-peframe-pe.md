# Q139 — peframe on Windows EXE

| Field | Value |
|---|---|
| **Target** | (local sample files) |
| **Domain** | 06 — Malware / Forensics |
| **Difficulty** | 🟡 Medium |
| **Tools** | `strings`, `file`, `peframe`, `olevba`, `yara`, `foremost` |
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
1. Verify VMs running: ping 192.168.52.129 and ping 192.168.52.130 from 192.168.52.128.
2. Required services for this Q already up by default on the relevant target.
3. If something is down, restart it on the target VM.

Report back: "Lab ready for Q139".
```
