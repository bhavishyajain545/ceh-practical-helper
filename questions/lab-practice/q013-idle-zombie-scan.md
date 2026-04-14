# Q013 — Idle (Zombie) Scan Concept

| Field | Value |
|---|---|
| **Target** | `192.168.52.129` (Metasploitable 2); `192.168.52.130` (Windows 7) |
| **Domain** | 02 — Scanning |
| **Difficulty** | 🔴 Hard |
| **Tools** | `nmap`, `hping3`, `masscan`, `nc` |
| **Time budget** | 5–10 min |

---

## 📝 Question

Use the Windows 7 host as the **zombie** to scan Metasploitable port 80 via idle scan. Provide the nmap command syntax.

---

## 🎯 Flag Format

```
cmd=<nmap-cmd>
```

Example: `cmd=nmap -sI 192.168.52.130 192.168.52.129 -p 80`

**⚠️ Format strict hai** — lowercase, no spaces around `=`, semicolon separator where shown.

---

## 💡 Hints

<details>
<summary>Hint 1 (click)</summary>

`-sI <zombie> <target>` triggers idle scan.
</details>

<details>
<summary>Hint 2</summary>

Zombie must have predictable IPID — Win7 typically OK on isolated lab.
</details>

---

## ✅ Solution

<details>
<summary>Show solution</summary>

```bash
sudo nmap -Pn -sI 192.168.52.130 192.168.52.129 -p 80
```

If IPID is incremental, scan reports port 80 open without source revealing.

**Answer:** `cmd=nmap -sI 192.168.52.130 192.168.52.129 -p 80`

📖 Ref: [tools/nmap.md](../../tools/nmap.md)
</details>

---

## 🤖 Claude Setup Prompt (for Claude-on-your-PC)

```
1. Win7 idle (no traffic) for clean IPID.

Report back: "Lab ready for Q013".
```
