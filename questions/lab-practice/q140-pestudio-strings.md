# Q140 — PEStudio / strings Indicators

| Field | Value |
|---|---|
| **Target** | (local sample files) |
| **Domain** | 06 — Malware / Forensics |
| **Difficulty** | 🟡 Medium |
| **Tools** | `strings (pestudio is Windows-only)` |
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
Pre-requisites for Q140:
1. Local Parrot exercise (pestudio is Windows GUI — on Parrot use `strings -a sample.exe | grep -i virtualalloc` as substitute).
2. Stage sample.exe (any PE, e.g. a Win putty.exe copied via scp).

Report back: "Lab ready for Q140 — strings available, sample.exe staged (pestudio optional on Win7)".
```
