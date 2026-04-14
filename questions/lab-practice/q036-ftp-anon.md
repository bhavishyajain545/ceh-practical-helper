# Q036 — FTP Anonymous Login Probe

| Field | Value |
|---|---|
| **Target** | `192.168.52.129` (Metasploitable 2) |
| **Domain** | 03 — Enumeration |
| **Difficulty** | 🟢 Easy |
| **Tools** | `nmap` |
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
Pre-requisites for Q036:
1. Verify Metasploitable2 (192.168.52.129) up; vsftpd running (sudo service vsftpd status).
2. From Parrot: nmap -p 21 192.168.52.129 — open.
3. Metasploitable2 vsftpd has anonymous DISABLED by default (expected answer 'no') — do not modify /etc/vsftpd.conf.

Report back: "Lab ready for Q036 — FTP 21 open on 192.168.52.129, anon disabled".
```
