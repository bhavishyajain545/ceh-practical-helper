# Q103 — Tomcat Default Creds Lookup

| Field | Value |
|---|---|
| **Target** | `192.168.52.129` / `192.168.52.130` |
| **Domain** | 05 — Credential Attacks |
| **Difficulty** | 🟢 Easy |
| **Tools** | `curl (or browser)` |
| **Time budget** | 10–20 min |

---

## 📝 Question

What is the default credential pair for Tomcat 5.5 on Metasploitable?

---

## 🎯 Flag Format

```
user=<u>; pass=<p>
```

Example: `user=tomcat; pass=tomcat`

**⚠️ Format strict hai** — lowercase, no spaces around `=`, semicolon separator where shown.

---

## 💡 Hints

<details>
<summary>Hint 1 (click)</summary>

Documented default.
</details>

<details>
<summary>Hint 2</summary>

Also `admin:admin`, `manager:manager`.
</details>

---

## ✅ Solution

<details>
<summary>Show solution</summary>

```bash
# Try at http://192.168.52.129:8180/manager/html
```

tomcat:tomcat works.

**Answer:** `user=tomcat; pass=tomcat`

📖 Ref: [tools/default-creds.md](../../tools/default-creds.md)
</details>

---

## 🤖 Claude Setup Prompt (for Claude-on-your-PC)

```
Pre-requisites for Q103:
1. Recall Q — no real login needed, but if verifying: Metasploitable2 up, Tomcat running.
2. curl -u tomcat:tomcat http://192.168.52.129:8180/manager/html returns 200.

Report back: "Lab ready for Q103 — Tomcat manager accessible with tomcat:tomcat (optional)".
```
