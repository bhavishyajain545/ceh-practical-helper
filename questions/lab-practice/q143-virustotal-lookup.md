# Q143 — VirusTotal Hash Lookup (Concept)

| Field | Value |
|---|---|
| **Target** | (local sample files) |
| **Domain** | 06 — Malware / Forensics |
| **Difficulty** | 🟢 Easy |
| **Tools** | `strings`, `file`, `peframe`, `olevba`, `yara`, `foremost` |
| **Time budget** | 10–15 min |

---

## 📝 Question

You have file md5 `44d88612fea8a8f36de82e1278abb02f` (EICAR). What does VT return as detection name?

---

## 🎯 Flag Format

```
name=<text>
```

Example: `name=EICAR-Test-File`

**⚠️ Format strict hai** — lowercase, no spaces around `=`, semicolon separator where shown.

---

## 💡 Hints

<details>
<summary>Hint 1 (click)</summary>

EICAR is a standard AV test string.
</details>

<details>
<summary>Hint 2</summary>

Hash the file then submit to VT.
</details>

---

## ✅ Solution

<details>
<summary>Show solution</summary>

```bash
md5sum eicar.com
```

VT returns EICAR-Test-File detections from all AVs.

**Answer:** `name=EICAR-Test-File`

📖 Ref: [playbooks/malware-playbook.md](../../playbooks/malware-playbook.md)
</details>

---

## 🤖 Claude Setup Prompt (for Claude-on-your-PC)

```
1. Verify VMs running: ping 192.168.52.129 and ping 192.168.52.130 from 192.168.52.128.
2. Required services for this Q already up by default on the relevant target.
3. If something is down, restart it on the target VM.

Report back: "Lab ready for Q143".
```
