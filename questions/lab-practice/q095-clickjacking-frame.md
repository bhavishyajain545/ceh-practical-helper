# Q095 — Clickjacking via iframe (Concept)

| Field | Value |
|---|---|
| **Target** | `192.168.52.129` (Metasploitable 2) |
| **Domain** | 12 — Web Apps / 13 — SQLi |
| **Difficulty** | 🟡 Medium |
| **Tools** | `browser` |
| **Time budget** | 15–25 min |

---

## 📝 Question

DVWA does not set `X-Frame-Options`. Provide HTML iframe snippet that embeds DVWA.

---

## 🎯 Flag Format

```
snippet=<html>
```

Example: `snippet=<iframe src='http://192.168.52.129/dvwa/'></iframe>`

**⚠️ Format strict hai** — lowercase, no spaces around `=`, semicolon separator where shown.

---

## 💡 Hints

<details>
<summary>Hint 1 (click)</summary>

Lack of X-Frame-Options/CSP frame-ancestors allows framing.
</details>

<details>
<summary>Hint 2</summary>

Layer with transparent button to clickjack.
</details>

---

## ✅ Solution

<details>
<summary>Show solution</summary>

```bash
<iframe src='http://192.168.52.129/dvwa/' width=800 height=600></iframe>
```

Page renders inside attacker iframe — clickjacking PoC.

**Answer:** `snippet=<iframe src='http://192.168.52.129/dvwa/'></iframe>`

📖 Ref: [playbooks/webapp-playbook.md](../../playbooks/webapp-playbook.md)
</details>

---

## 🤖 Claude Setup Prompt (for Claude-on-your-PC)

```
Pre-requisites for Q095:
1. DVWA up; does not set X-Frame-Options (Metasploitable2 default).
2. Host a small HTML file via python3 -m http.server on Parrot and open in Firefox.

Report back: "Lab ready for Q095 — DVWA reachable, no X-Frame-Options header".
```
