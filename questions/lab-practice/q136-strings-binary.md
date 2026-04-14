# Q136 — strings on Suspicious Binary

| Field | Value |
|---|---|
| **Target** | (local sample files) |
| **Domain** | 06 — Malware / Forensics |
| **Difficulty** | 🟡 Medium |
| **Tools** | `strings`, `file`, `peframe`, `olevba`, `yara`, `foremost` |
| **Time budget** | 10–15 min |

---

## 📝 Question

Run `strings -n 6 suspicious.bin` and grep for URLs. Provide command.

---

## 🎯 Flag Format

```
cmd=<short>
```

Example: `cmd=strings -n 6 suspicious.bin | grep -E 'https?://'`

**⚠️ Format strict hai** — lowercase, no spaces around `=`, semicolon separator where shown.

---

## 💡 Hints

<details>
<summary>Hint 1 (click)</summary>

`-n 6` minimum length.
</details>

<details>
<summary>Hint 2</summary>

Pipe through grep.
</details>

---

## ✅ Solution

<details>
<summary>Show solution</summary>

```bash
strings -n 6 suspicious.bin | grep -E 'https?://'
```

Lists embedded URLs.

**Answer:** `cmd=strings -n 6 suspicious.bin | grep -E 'https?://'`

📖 Ref: [playbooks/forensics-playbook.md](../../playbooks/forensics-playbook.md)
</details>

---

## 🤖 Claude Setup Prompt (for Claude-on-your-PC)

```
1. Verify VMs running: ping 192.168.52.129 and ping 192.168.52.130 from 192.168.52.128.
2. Required services for this Q already up by default on the relevant target.
3. If something is down, restart it on the target VM.

Report back: "Lab ready for Q136".
```
