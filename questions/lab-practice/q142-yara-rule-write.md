# Q142 — Write a Simple YARA Rule

| Field | Value |
|---|---|
| **Target** | (local sample files) |
| **Domain** | 06 — Malware / Forensics |
| **Difficulty** | 🟡 Medium |
| **Tools** | `strings`, `file`, `peframe`, `olevba`, `yara`, `foremost` |
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
1. Verify VMs running: ping 192.168.52.129 and ping 192.168.52.130 from 192.168.52.128.
2. Required services for this Q already up by default on the relevant target.
3. If something is down, restart it on the target VM.

Report back: "Lab ready for Q142".
```
