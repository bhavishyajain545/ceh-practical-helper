# Q140 — PEStudio / strings Indicators

| Field | Value |
|---|---|
| **Target** | (local sample files) |
| **Domain** | 06 — Malware / Forensics |
| **Difficulty** | 🟡 Medium |
| **Tools** | `strings`, `file`, `peframe`, `olevba`, `yara`, `foremost` |
| **Time budget** | 10–15 min |

---

## 📝 Question

Identify a common API name suspicious for malware in PE imports.

---

## 🎯 Flag Format

```
api=<name>
```

Example: `api=VirtualAllocEx`

**⚠️ Format strict hai** — lowercase, no spaces around `=`, semicolon separator where shown.

---

## 💡 Hints

<details>
<summary>Hint 1 (click)</summary>

Look for memory/inject APIs.
</details>

<details>
<summary>Hint 2</summary>

VirtualAllocEx, WriteProcessMemory, CreateRemoteThread.
</details>

---

## ✅ Solution

<details>
<summary>Show solution</summary>

```bash
# pestudio sample.exe
```

VirtualAllocEx flagged as suspicious.

**Answer:** `api=VirtualAllocEx`

📖 Ref: [playbooks/malware-playbook.md](../../playbooks/malware-playbook.md)
</details>

---

## 🤖 Claude Setup Prompt (for Claude-on-your-PC)

```
1. Verify VMs running: ping 192.168.52.129 and ping 192.168.52.130 from 192.168.52.128.
2. Required services for this Q already up by default on the relevant target.
3. If something is down, restart it on the target VM.

Report back: "Lab ready for Q140".
```
