# Q142 — Write a Simple YARA Rule

| Field | Value |
|---|---|
| **Target** | (local sample files) |
| **Domain** | 06 — Malware / Forensics |
| **Difficulty** | 🟡 Medium |
| **Tools** | `yara` |
| **Time budget** | 10–15 min |

---

## 📝 Question

Write a YARA rule named `find_cehlab` matching string `CEHLAB` in any file.

---

## 🎯 Flag Format

```
rule_body=<text>
```

Example: `rule_body=rule find_cehlab { strings: $a = "CEHLAB" condition: $a }`

**⚠️ Format strict hai** — lowercase, no spaces around `=`, semicolon separator where shown.

---

## 💡 Hints

<details>
<summary>Hint 1 (click)</summary>

YARA rule has meta/strings/condition.
</details>

<details>
<summary>Hint 2</summary>

Run with `yara rule.yar <dir>`.
</details>

---

## ✅ Solution

<details>
<summary>Show solution</summary>

```bash
cat > r.yar <<EOF
rule find_cehlab { strings: \$a = "CEHLAB" condition: \$a }
EOF
yara r.yar /tmp/
```

Matches files containing `CEHLAB`.

**Answer:** `rule_body=rule find_cehlab { strings: $a = "CEHLAB" condition: $a }`

📖 Ref: [playbooks/malware-playbook.md](../../playbooks/malware-playbook.md)
</details>

---

## 🤖 Claude Setup Prompt (for Claude-on-your-PC)

```
Pre-requisites for Q142:
1. Local Parrot exercise.
2. which yara (sudo apt install yara).
3. Have a /tmp test file containing 'CEHLAB' for match: echo CEHLAB > /tmp/test.txt.

Report back: "Lab ready for Q142 — yara installed, /tmp/test.txt contains CEHLAB".
```
