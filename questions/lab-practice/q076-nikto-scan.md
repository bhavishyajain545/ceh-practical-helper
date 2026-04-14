# Q076 — Nikto Web Scan

| Field | Value |
|---|---|
| **Target** | `192.168.52.129` (Metasploitable 2) |
| **Domain** | 12 — Web Apps / 13 — SQLi |
| **Difficulty** | 🟡 Medium |
| **Tools** | `burpsuite`, `sqlmap`, `gobuster`, `nikto`, `curl` |
| **Time budget** | 15–25 min |

---

## 📝 Question

Run nikto against Metasploitable port 80. Report **any one OSVDB ID** flagged.

---

## 🎯 Flag Format

```
osvdb=<id-or-finding>
```

Example: `osvdb=OSVDB-3268`

**⚠️ Format strict hai** — lowercase, no spaces around `=`, semicolon separator where shown.

---

## 💡 Hints

<details>
<summary>Hint 1 (click)</summary>

`nikto -h http://<ip>`
</details>

<details>
<summary>Hint 2</summary>

Many findings — pick any.
</details>

---

## ✅ Solution

<details>
<summary>Show solution</summary>

```bash
nikto -h http://192.168.52.129/
```

Reports many issues including OSVDB-3268 (directory indexing).

**Answer:** `osvdb=OSVDB-3268`

📖 Ref: [tools/nikto.md](../../tools/nikto.md)
</details>

---

## 🤖 Claude Setup Prompt (for Claude-on-your-PC)

```
1. Verify VMs running: ping 192.168.52.129 and ping 192.168.52.130 from 192.168.52.128.
2. Required services for this Q already up by default on the relevant target.
3. If something is down, restart it on the target VM.

Report back: "Lab ready for Q076".
```
