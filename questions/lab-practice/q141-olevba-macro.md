# Q141 — olevba on Macro Doc

| Field | Value |
|---|---|
| **Target** | (local sample files) |
| **Domain** | 06 — Malware / Forensics |
| **Difficulty** | 🔴 Hard |
| **Tools** | `olevba` |
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
Pre-requisites for Q141:
1. Local Parrot exercise.
2. Install: pip3 install oletools.
3. Stage macro.docm sample — create via LibreOffice on Parrot, or download a known-benign macro sample.

Report back: "Lab ready for Q141 — olevba installed, macro.docm staged".
```
