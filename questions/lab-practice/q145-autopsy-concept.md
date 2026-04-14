# Q145 — Autopsy Forensic Browser Concept

| Field | Value |
|---|---|
| **Target** | (local sample files) |
| **Domain** | 06 — Malware / Forensics |
| **Difficulty** | 🟢 Easy |
| **Tools** | `strings`, `file`, `peframe`, `olevba`, `yara`, `foremost` |
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
1. Verify VMs running: ping 192.168.52.129 and ping 192.168.52.130 from 192.168.52.128.
2. Required services for this Q already up by default on the relevant target.
3. If something is down, restart it on the target VM.

Report back: "Lab ready for Q145".
```
