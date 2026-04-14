# Q031 — HTTP Methods Enum

| Field | Value |
|---|---|
| **Target** | `192.168.52.129` (Metasploitable 2) |
| **Domain** | 03 — Enumeration |
| **Difficulty** | 🟢 Easy |
| **Tools** | `nmap` |
| **Time budget** | 10–15 min |

---

## 📝 Question

Use `nmap --script http-methods -p 80 192.168.52.129` to identify allowed HTTP methods. Is **TRACE** allowed?

---

## 🎯 Flag Format

```
trace=<yes|no>
```

Example: `trace=yes`

**⚠️ Format strict hai** — lowercase, no spaces around `=`, semicolon separator where shown.

---

## 💡 Hints

<details>
<summary>Hint 1 (click)</summary>

Apache 2.2.8 default config has TRACE enabled.
</details>

<details>
<summary>Hint 2</summary>

Look for `Methods supported`.
</details>

---

## ✅ Solution

<details>
<summary>Show solution</summary>

```bash
nmap --script http-methods --script-args http-methods.url-path=/ -p 80 192.168.52.129
```

TRACE shown as supported (XST risk).

**Answer:** `trace=yes`

📖 Ref: [tools/nmap.md](../../tools/nmap.md)
</details>

---

## 🤖 Claude Setup Prompt (for Claude-on-your-PC)

```
Pre-requisites for Q031:
1. Verify Metasploitable2 (192.168.52.129) up; apache2 running (sudo service apache2 status).
2. From Parrot: curl -I http://192.168.52.129/ — must return HTTP 200/OK.
3. TRACE is enabled by default in the Metasploitable2 Apache config — confirm no patch applied.

Report back: "Lab ready for Q031 — HTTP 80 open on 192.168.52.129, TRACE enabled".
```
