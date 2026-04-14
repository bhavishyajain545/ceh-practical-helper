# Q110 — Password Policy via rpcclient

| Field | Value |
|---|---|
| **Target** | `192.168.52.129` / `192.168.52.130` |
| **Domain** | 05 — Credential Attacks |
| **Difficulty** | 🟡 Medium |
| **Tools** | `hydra`, `john`, `hashcat`, `crackmapexec`, `responder` |
| **Time budget** | 10–20 min |

---

## 📝 Question

Use rpcclient `getdompwinfo` against Metasploitable to read password policy. Report **min password length**.

---

## 🎯 Flag Format

```
min_length=<n>
```

Example: `min_length=5`

**⚠️ Format strict hai** — lowercase, no spaces around `=`, semicolon separator where shown.

---

## 💡 Hints

<details>
<summary>Hint 1 (click)</summary>

`rpcclient -U "" -N 192.168.52.129 -c 'getdompwinfo'`
</details>

<details>
<summary>Hint 2</summary>

Default Samba returns small min length.
</details>

---

## ✅ Solution

<details>
<summary>Show solution</summary>

```bash
rpcclient -U "" -N 192.168.52.129 -c 'getdompwinfo'
```

Min Password Length: 5 (default).

**Answer:** `min_length=5`

📖 Ref: [tools/enum4linux.md](../../tools/enum4linux.md)
</details>

---

## 🤖 Claude Setup Prompt (for Claude-on-your-PC)

```
1. Verify VMs running: ping 192.168.52.129 and ping 192.168.52.130 from 192.168.52.128.
2. Required services for this Q already up by default on the relevant target.
3. If something is down, restart it on the target VM.

Report back: "Lab ready for Q110".
```
