# Q145 — Autopsy Forensic Browser Concept

| Field | Value |
|---|---|
| **Target** | (local sample files) |
| **Domain** | 06 — Malware / Forensics |
| **Difficulty** | 🟢 Easy |
| **Tools** | `(Autopsy - recall)` |
| **Time budget** | 10–15 min |

---

## 📝 Question

Name the open-source GUI forensic suite built on The Sleuth Kit.

---

## 🎯 Flag Format

```
tool=<name>
```

Example: `tool=Autopsy`

**⚠️ Format strict hai** — lowercase, no spaces around `=`, semicolon separator where shown.

---

## 💡 Hints

<details>
<summary>Hint 1 (click)</summary>

Web/desktop GUI for TSK.
</details>

<details>
<summary>Hint 2</summary>

Used widely in DFIR.
</details>

---

## ✅ Solution

<details>
<summary>Show solution</summary>

```bash
# autopsy
```

Autopsy.

**Answer:** `tool=Autopsy`

📖 Ref: [playbooks/forensics-playbook.md](../../playbooks/forensics-playbook.md)
</details>

---

## 🤖 Claude Setup Prompt (for Claude-on-your-PC)

```
Pre-requisites for Q145:
1. Conceptual/recall Q — no repro.
2. Optional: on Parrot `sudo apt install autopsy` to demonstrate.

Report back: "Lab ready for Q145 — conceptual Q, no lab state required".
```
