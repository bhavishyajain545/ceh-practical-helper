# Q137 — file Command Identification

| Field | Value |
|---|---|
| **Target** | (local sample files) |
| **Domain** | 06 — Malware / Forensics |
| **Difficulty** | 🟢 Easy |
| **Tools** | `strings`, `file`, `peframe`, `olevba`, `yara`, `foremost` |
| **Time budget** | 10–15 min |

---

## 📝 Question

Run `file` on a sample. What does ELF 64-bit dynamically linked Linux executable show?

---

## 🎯 Flag Format

```
label=<text>
```

Example: `label=ELF 64-bit LSB executable`

**⚠️ Format strict hai** — lowercase, no spaces around `=`, semicolon separator where shown.

---

## 💡 Hints

<details>
<summary>Hint 1 (click)</summary>

`file <name>`
</details>

<details>
<summary>Hint 2</summary>

Outputs format/arch/linkage.
</details>

---

## ✅ Solution

<details>
<summary>Show solution</summary>

```bash
file /bin/ls
```

`ELF 64-bit LSB ... dynamically linked ...`.

**Answer:** `label=ELF 64-bit LSB executable`

📖 Ref: [tools/file.md](../../tools/file.md)
</details>

---

## 🤖 Claude Setup Prompt (for Claude-on-your-PC)

```
1. Verify VMs running: ping 192.168.52.129 and ping 192.168.52.130 from 192.168.52.128.
2. Required services for this Q already up by default on the relevant target.
3. If something is down, restart it on the target VM.

Report back: "Lab ready for Q137".
```
