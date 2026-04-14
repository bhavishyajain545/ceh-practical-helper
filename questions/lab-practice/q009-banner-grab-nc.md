# Q009 — Banner Grabbing with Netcat

| Field | Value |
|---|---|
| **Target** | `192.168.52.129` (Metasploitable 2) |
| **Domain** | 02 — Scanning |
| **Difficulty** | 🟡 Medium |
| **Tools** | `nmap`, `hping3`, `masscan`, `nc` |
| **Time budget** | 5–10 min |

---

## 📝 Question

Use `nc` to grab banners from Metasploitable ports 21, 22, and 25. Report the **SMTP banner program name**.

---

## 🎯 Flag Format

```
smtp_banner=<program>
```

Example: `smtp_banner=Postfix`

**⚠️ Format strict hai** — lowercase, no spaces around `=`, semicolon separator where shown.

---

## 💡 Hints

<details>
<summary>Hint 1 (click)</summary>

`nc <ip> 25` and read the 220 line.
</details>

<details>
<summary>Hint 2</summary>

Metasploitable runs Postfix smtpd.
</details>

---

## ✅ Solution

<details>
<summary>Show solution</summary>

```bash
nc 192.168.52.129 25
```

220 banner identifies **Postfix** SMTP.

**Answer:** `smtp_banner=Postfix`

📖 Ref: [tools/nmap.md](../../tools/nmap.md)
</details>

---

## 🤖 Claude Setup Prompt (for Claude-on-your-PC)

```
1. Metasploitable postfix running.

Report back: "Lab ready for Q009".
```
