# Q105 — CrackMapExec SMB Spray

| Field | Value |
|---|---|
| **Target** | `192.168.52.130` (Windows 7) |
| **Domain** | 05 — Credential Attacks |
| **Difficulty** | 🟡 Medium |
| **Tools** | `hydra`, `john`, `hashcat`, `crackmapexec`, `responder` |
| **Time budget** | 10–20 min |

---

## 📝 Question

Use crackmapexec to test creds `Administrator:password` on Win7 SMB. Provide command.

---

## 🎯 Flag Format

```
cmd=<short>
```

Example: `cmd=crackmapexec smb 192.168.52.130 -u Administrator -p password`

**⚠️ Format strict hai** — lowercase, no spaces around `=`, semicolon separator where shown.

---

## 💡 Hints

<details>
<summary>Hint 1 (click)</summary>

Or `cme smb`.
</details>

<details>
<summary>Hint 2</summary>

Returns `[+]` on success.
</details>

---

## ✅ Solution

<details>
<summary>Show solution</summary>

```bash
crackmapexec smb 192.168.52.130 -u Administrator -p password
```

Validates auth and shows hostname/domain.

**Answer:** `cmd=crackmapexec smb 192.168.52.130 -u Administrator -p password`

📖 Ref: [tools/crackmapexec.md](../../tools/crackmapexec.md)
</details>

---

## 🤖 Claude Setup Prompt (for Claude-on-your-PC)

```
1. Verify VMs running: ping 192.168.52.129 and ping 192.168.52.130 from 192.168.52.128.
2. Required services for this Q already up by default on the relevant target.
3. If something is down, restart it on the target VM.

Report back: "Lab ready for Q105".
```
