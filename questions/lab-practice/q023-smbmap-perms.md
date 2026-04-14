# Q023 — smbmap Permission Mapping

| Field | Value |
|---|---|
| **Target** | `192.168.52.129` (Metasploitable 2) |
| **Domain** | 03 — Enumeration |
| **Difficulty** | 🟢 Easy |
| **Tools** | `enum4linux`, `smbclient`, `rpcclient`, `nmap NSE` |
| **Time budget** | 10–15 min |

---

## 📝 Question

Run `smbmap -H 192.168.52.129` and report which share has **READ, WRITE** permissions.

---

## 🎯 Flag Format

```
rw_share=<name>
```

Example: `rw_share=tmp`

**⚠️ Format strict hai** — lowercase, no spaces around `=`, semicolon separator where shown.

---

## 💡 Hints

<details>
<summary>Hint 1 (click)</summary>

`smbmap -H <ip>` enumerates shares + ACLs without creds.
</details>

<details>
<summary>Hint 2</summary>

READ, WRITE → world-writable share.
</details>

---

## ✅ Solution

<details>
<summary>Show solution</summary>

```bash
smbmap -H 192.168.52.129
```

`tmp` share is READ, WRITE.

**Answer:** `rw_share=tmp`

📖 Ref: [tools/enum4linux.md](../../tools/enum4linux.md)
</details>

---

## 🤖 Claude Setup Prompt (for Claude-on-your-PC)

```
1. Verify VMs running: ping 192.168.52.129 and ping 192.168.52.130 from 192.168.52.128.
2. Required services for this Q already up by default on the relevant target.
3. If something is down, restart it on the target VM.

Report back: "Lab ready for Q023".
```
