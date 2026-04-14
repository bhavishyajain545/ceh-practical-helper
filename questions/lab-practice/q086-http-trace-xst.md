# Q086 — Cross-Site Tracing (XST)

| Field | Value |
|---|---|
| **Target** | `192.168.52.129` (Metasploitable 2) |
| **Domain** | 12 — Web Apps / 13 — SQLi |
| **Difficulty** | 🟢 Easy |
| **Tools** | `curl` |
| **Time budget** | 15–25 min |

---

## 📝 Question

Send a TRACE request to Metasploitable's Apache. What does the server echo back?

---

## 🎯 Flag Format

```
echo=<headers|body|both>
```

Example: `echo=both`

**⚠️ Format strict hai** — lowercase, no spaces around `=`, semicolon separator where shown.

---

## 💡 Hints

<details>
<summary>Hint 1 (click)</summary>

`curl -X TRACE http://192.168.52.129/`
</details>

<details>
<summary>Hint 2</summary>

Apache echoes the entire request.
</details>

---

## ✅ Solution

<details>
<summary>Show solution</summary>

```bash
curl -X TRACE http://192.168.52.129/ -H 'X-Test: pwned'
```

Server echoes the full request including custom header — XST PoC.

**Answer:** `echo=both`

📖 Ref: [tools/curl.md](../../tools/curl.md)
</details>

---

## 🤖 Claude Setup Prompt (for Claude-on-your-PC)

```
Pre-requisites for Q086:
1. Verify Metasploitable2 up; apache2 running with TRACE enabled (Metasploitable2 default).
2. curl -X TRACE http://192.168.52.129/ returns 200 with echoed request.

Report back: "Lab ready for Q086 — TRACE method enabled on 192.168.52.129:80".
```
