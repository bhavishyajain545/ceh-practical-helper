# Q075 — gobuster Directory Brute

| Field | Value |
|---|---|
| **Target** | `192.168.52.129` (Metasploitable 2) |
| **Domain** | 12 — Web Apps / 13 — SQLi |
| **Difficulty** | 🟡 Medium |
| **Tools** | `burpsuite`, `sqlmap`, `gobuster`, `nikto`, `curl` |
| **Time budget** | 15–25 min |

---

## 📝 Question

Use gobuster on `http://192.168.52.129/` with `dirb common.txt`. Report **two** discovered paths.

---

## 🎯 Flag Format

```
paths=<p1>,<p2>
```

Example: `paths=/dvwa,/phpMyAdmin`

**⚠️ Format strict hai** — lowercase, no spaces around `=`, semicolon separator where shown.

---

## 💡 Hints

<details>
<summary>Hint 1 (click)</summary>

`gobuster dir -u <url> -w wordlist`
</details>

<details>
<summary>Hint 2</summary>

Common.txt comes with dirb.
</details>

---

## ✅ Solution

<details>
<summary>Show solution</summary>

```bash
gobuster dir -u http://192.168.52.129/ -w /usr/share/wordlists/dirb/common.txt
```

Finds /dvwa, /phpMyAdmin, /mutillidae, /tikiwiki, /test, /twiki etc.

**Answer:** `paths=/dvwa,/phpMyAdmin`

📖 Ref: [tools/gobuster.md](../../tools/gobuster.md)
</details>

---

## 🤖 Claude Setup Prompt (for Claude-on-your-PC)

```
1. Verify VMs running: ping 192.168.52.129 and ping 192.168.52.130 from 192.168.52.128.
2. Required services for this Q already up by default on the relevant target.
3. If something is down, restart it on the target VM.

Report back: "Lab ready for Q075".
```
