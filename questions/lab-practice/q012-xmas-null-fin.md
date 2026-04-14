# Q012 — Xmas, Null and FIN Scan

| Field | Value |
|---|---|
| **Target** | `192.168.52.129` (Metasploitable 2); `192.168.52.130` (Windows 7) |
| **Domain** | 02 — Scanning |
| **Difficulty** | 🟢 Easy |
| **Tools** | `nmap`, `hping3`, `masscan`, `nc` |
| **Time budget** | 5–10 min |

---

## 📝 Question

Run a Xmas scan (`-sX`) on Metasploitable port 22 and Windows 7 port 445. Which target classifies the port as **open|filtered** vs **closed**?

---

## 🎯 Flag Format

```
linux=<state>; windows=<state>
```

Example: `linux=open|filtered; windows=closed`

**⚠️ Format strict hai** — lowercase, no spaces around `=`, semicolon separator where shown.

---

## 💡 Hints

<details>
<summary>Hint 1 (click)</summary>

Linux follows RFC 793 — open ports drop Xmas packets (open|filtered).
</details>

<details>
<summary>Hint 2</summary>

Windows replies RST to all → closed.
</details>

---

## ✅ Solution

<details>
<summary>Show solution</summary>

```bash
sudo nmap -sX -p 22 192.168.52.129
sudo nmap -sX -p 445 192.168.52.130
```

Linux open|filtered, Windows closed — classic Xmas-scan behavior.

**Answer:** `linux=open|filtered; windows=closed`

📖 Ref: [tools/nmap.md](../../tools/nmap.md)
</details>

---

## 🤖 Claude Setup Prompt (for Claude-on-your-PC)

```
1. Both VMs up.

Report back: "Lab ready for Q012".
```
