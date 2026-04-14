# Q080 — Burp Suite Intercept

| Field | Value |
|---|---|
| **Target** | `192.168.52.129` (Metasploitable 2) |
| **Domain** | 12 — Web Apps / 13 — SQLi |
| **Difficulty** | 🟡 Medium |
| **Tools** | `burpsuite` |
| **Time budget** | 15–25 min |

---

## 📝 Question

Configure Burp to intercept DVWA login request. What **request method** and what is the **POST parameter** containing the password?

---

## 🎯 Flag Format

```
method=<POST|GET>; param=<name>
```

Example: `method=POST; param=password`

**⚠️ Format strict hai** — lowercase, no spaces around `=`, semicolon separator where shown.

---

## 💡 Hints

<details>
<summary>Hint 1 (click)</summary>

Set browser proxy to 127.0.0.1:8080.
</details>

<details>
<summary>Hint 2</summary>

DVWA login uses POST.
</details>

---

## ✅ Solution

<details>
<summary>Show solution</summary>

```bash
# Burp → Proxy → Intercept ON; submit DVWA login
```

Captures POST with `username` and `password` params.

**Answer:** `method=POST; param=password`

📖 Ref: [tools/burpsuite.md](../../tools/burpsuite.md)
</details>

---

## 🤖 Claude Setup Prompt (for Claude-on-your-PC)

```
Pre-requisites for Q080:
1. Verify Metasploitable2 (192.168.52.129) up; DVWA reachable.
2. Launch Burp on Parrot (burpsuite &). Configure Firefox proxy → 127.0.0.1:8080.
3. Install Burp CA cert in Firefox to avoid SSL errors.

Report back: "Lab ready for Q080 — DVWA reachable, Burp proxy on 127.0.0.1:8080".
```
