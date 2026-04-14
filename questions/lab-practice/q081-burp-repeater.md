# Q081 — Burp Repeater Session ID

| Field | Value |
|---|---|
| **Target** | `192.168.52.129` (Metasploitable 2) |
| **Domain** | 12 — Web Apps / 13 — SQLi |
| **Difficulty** | 🟡 Medium |
| **Tools** | `burpsuite`, `sqlmap`, `gobuster`, `nikto`, `curl` |
| **Time budget** | 15–25 min |

---

## 📝 Question

In Burp, send DVWA SQLi request to Repeater and modify cookie `security` from `low` to `high`. Note the response **status code**.

---

## 🎯 Flag Format

```
status=<code>
```

Example: `status=200`

**⚠️ Format strict hai** — lowercase, no spaces around `=`, semicolon separator where shown.

---

## 💡 Hints

<details>
<summary>Hint 1 (click)</summary>

Right-click → Send to Repeater.
</details>

<details>
<summary>Hint 2</summary>

Page returns 200 either way; behavior changes.
</details>

---

## ✅ Solution

<details>
<summary>Show solution</summary>

```bash
# Burp Repeater modify Cookie header
```

200 OK regardless of security setting.

**Answer:** `status=200`

📖 Ref: [tools/burpsuite.md](../../tools/burpsuite.md)
</details>

---

## 🤖 Claude Setup Prompt (for Claude-on-your-PC)

```
1. Verify VMs running: ping 192.168.52.129 and ping 192.168.52.130 from 192.168.52.128.
2. Required services for this Q already up by default on the relevant target.
3. If something is down, restart it on the target VM.

Report back: "Lab ready for Q081".
```
