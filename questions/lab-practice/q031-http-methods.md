# Q031 — HTTP Methods Enum

| Field | Value |
|---|---|
| **Target** | `192.168.52.129` (Metasploitable 2) |
| **Domain** | 03 — Enumeration |
| **Difficulty** | 🟢 Easy |
| **Tools** | `enum4linux`, `smbclient`, `rpcclient`, `nmap NSE` |
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
1. Verify VMs running: ping 192.168.52.129 and ping 192.168.52.130 from 192.168.52.128.
2. Required services for this Q already up by default on the relevant target.
3. If something is down, restart it on the target VM.

Report back: "Lab ready for Q031".
```
