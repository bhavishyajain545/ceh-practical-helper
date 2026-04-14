# Q085 — JWT None Algorithm (Concept)

| Field | Value |
|---|---|
| **Target** | `192.168.52.129` (Metasploitable 2) |
| **Domain** | 12 — Web Apps / 13 — SQLi |
| **Difficulty** | 🔴 Hard |
| **Tools** | `python3 (base64 util)` |
| **Time budget** | 15–25 min |

---

## 📝 Question

Given a JWT `eyJhbGciOiJIUzI1NiJ9.eyJ1c2VyIjoiYWRtaW4ifQ.sig`, craft a `none` algorithm variant. Provide the modified header (base64).

---

## 🎯 Flag Format

```
header_b64=<base64>
```

Example: `header_b64=eyJhbGciOiJub25lIn0`

**⚠️ Format strict hai** — lowercase, no spaces around `=`, semicolon separator where shown.

---

## 💡 Hints

<details>
<summary>Hint 1 (click)</summary>

`{"alg":"none"}` base64url encoded.
</details>

<details>
<summary>Hint 2</summary>

Strip signature, leave trailing dot.
</details>

---

## ✅ Solution

<details>
<summary>Show solution</summary>

```bash
echo -n '{"alg":"none"}' | base64 | tr -d '=' | tr '/+' '_-'
```

Header becomes `eyJhbGciOiJub25lIn0`; vulnerable libs accept it.

**Answer:** `header_b64=eyJhbGciOiJub25lIn0`

📖 Ref: [playbooks/webapp-playbook.md](../../playbooks/webapp-playbook.md)
</details>

---

## 🤖 Claude Setup Prompt (for Claude-on-your-PC)

```
Pre-requisites for Q085:
1. Local Parrot exercise — no VM.
2. Python3 available: which python3.

Report back: "Lab ready for Q085 — local Parrot shell with python3".
```
