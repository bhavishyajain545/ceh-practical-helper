# Q103 — Tomcat Default Creds Lookup

| Field | Value |
|---|---|
| **Target** | `192.168.52.129` / `192.168.52.130` |
| **Domain** | 05 — Credential Attacks |
| **Difficulty** | 🟢 Easy |
| **Tools** | `hydra`, `john`, `hashcat`, `crackmapexec`, `responder` |
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
1. Verify VMs running: ping 192.168.52.129 and ping 192.168.52.130 from 192.168.52.128.
2. Required services for this Q already up by default on the relevant target.
3. If something is down, restart it on the target VM.

Report back: "Lab ready for Q103".
```
