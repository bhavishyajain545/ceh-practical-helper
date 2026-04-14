# Q141 — olevba on Macro Doc

| Field | Value |
|---|---|
| **Target** | (local sample files) |
| **Domain** | 06 — Malware / Forensics |
| **Difficulty** | 🔴 Hard |
| **Tools** | `strings`, `file`, `peframe`, `olevba`, `yara`, `foremost` |
| **Time budget** | 10–15 min |

---

## 📝 Question

Run `olevba` against `macro.docm`. What flag dumps decoded macro?

---

## 🎯 Flag Format

```
flag=<flag>
```

Example: `flag=--decode`

**⚠️ Format strict hai** — lowercase, no spaces around `=`, semicolon separator where shown.

---

## 💡 Hints

<details>
<summary>Hint 1 (click)</summary>

`olevba macro.docm`
</details>

<details>
<summary>Hint 2</summary>

`--decode` decodes obfuscation.
</details>

---

## ✅ Solution

<details>
<summary>Show solution</summary>

```bash
olevba --decode macro.docm
```

Prints VBA source + IOCs.

**Answer:** `flag=--decode`

📖 Ref: [playbooks/malware-playbook.md](../../playbooks/malware-playbook.md)
</details>

---

## 🤖 Claude Setup Prompt (for Claude-on-your-PC)

```
1. Verify VMs running: ping 192.168.52.129 and ping 192.168.52.130 from 192.168.52.128.
2. Required services for this Q already up by default on the relevant target.
3. If something is down, restart it on the target VM.

Report back: "Lab ready for Q141".
```
