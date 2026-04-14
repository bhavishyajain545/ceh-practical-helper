# Q143 — VirusTotal Hash Lookup (Concept)

| Field | Value |
|---|---|
| **Target** | (local sample files) |
| **Domain** | 06 — Malware / Forensics |
| **Difficulty** | 🟢 Easy |
| **Tools** | `browser (virustotal.com)` |
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
Pre-requisites for Q143:
1. Conceptual Q — requires Parrot internet access to virustotal.com.
2. VT free web lookup works without API key.
3. Optional: stage eicar.com: echo 'X5O!P%@AP[4\PZX54(P^)7CC)7}$EICAR-STANDARD-ANTIVIRUS-TEST-FILE!$H+H*' > eicar.com.

Report back: "Lab ready for Q143 — Parrot internet to virustotal.com, eicar.com staged".
```
