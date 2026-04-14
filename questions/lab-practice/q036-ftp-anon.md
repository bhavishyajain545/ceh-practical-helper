# Q036 — FTP Anonymous Login Probe

| Field | Value |
|---|---|
| **Target** | `192.168.52.129` (Metasploitable 2) |
| **Domain** | 03 — Enumeration |
| **Difficulty** | 🟢 Easy |
| **Tools** | `enum4linux`, `smbclient`, `rpcclient`, `nmap NSE` |
| **Time budget** | 10–15 min |

---

## 📝 Question

Use NSE `ftp-anon` on Metasploitable port 21. Is anonymous FTP allowed?

---

## 🎯 Flag Format

```
ftp_anon=<yes|no>
```

Example: `ftp_anon=no`

**⚠️ Format strict hai** — lowercase, no spaces around `=`, semicolon separator where shown.

---

## 💡 Hints

<details>
<summary>Hint 1 (click)</summary>

`nmap --script ftp-anon -p 21 <ip>`
</details>

<details>
<summary>Hint 2</summary>

vsftpd 2.3.4 with default config in Metasploitable disallows anon.
</details>

---

## ✅ Solution

<details>
<summary>Show solution</summary>

```bash
nmap --script ftp-anon -p 21 192.168.52.129
```

Reports anonymous FTP login disabled.

**Answer:** `ftp_anon=no`

📖 Ref: [tools/nmap.md](../../tools/nmap.md)
</details>

---

## 🤖 Claude Setup Prompt (for Claude-on-your-PC)

```
1. Verify VMs running: ping 192.168.52.129 and ping 192.168.52.130 from 192.168.52.128.
2. Required services for this Q already up by default on the relevant target.
3. If something is down, restart it on the target VM.

Report back: "Lab ready for Q036".
```
