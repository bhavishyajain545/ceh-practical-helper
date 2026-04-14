# Q009 — Banner Grabbing with Netcat

| Field | Value |
|---|---|
| **Target** | `192.168.52.129` (Metasploitable 2) |
| **Domain** | 02 — Scanning |
| **Difficulty** | 🟡 Medium |
| **Tools** | `nc` |
| **Time budget** | 5–10 min |

---

## 📝 Question

Use `nc` to grab banners from Metasploitable ports 21, 22, and 25. Report the **SMTP banner program name**.

---

## 🎯 Flag Format

```
smtp_banner=<program>
```

Example: `smtp_banner=Postfix`

**⚠️ Format strict hai** — lowercase, no spaces around `=`, semicolon separator where shown.

---

## 💡 Hints

<details>
<summary>Hint 1 (click)</summary>

`nc <ip> 25` and read the 220 line.
</details>

<details>
<summary>Hint 2</summary>

Metasploitable runs Postfix smtpd.
</details>

---

## ✅ Solution

<details>
<summary>Show solution</summary>

```bash
nc 192.168.52.129 25
```

220 banner identifies **Postfix** SMTP.

**Answer:** `smtp_banner=Postfix`

📖 Ref: [tools/nmap.md](../../tools/nmap.md)
</details>

---

## 🤖 Claude Setup Prompt (for Claude-on-your-PC)

```
Pre-requisites for Q009:
1. Verify Metasploitable2 (192.168.52.129) is reachable: ping -c 2 192.168.52.129.
2. Confirm nc on Parrot: which nc → /usr/bin/nc (usually nc.traditional or ncat symlinked). Either flavour works for this banner grab.
3. Target services — all three must be listening or nc will just time out:
   - 21 vsftpd: nc 192.168.52.129 21 → "220 (vsFTPd 2.3.4)"
   - 22 OpenSSH: nc 192.168.52.129 22 → "SSH-2.0-OpenSSH_4.7p1 Debian-8ubuntu1"
   - 25 Postfix: nc 192.168.52.129 25 → "220 metasploitable.localdomain ESMTP Postfix ..."
   - If any missing: ssh msfadmin@192.168.52.129 → sudo service vsftpd|ssh|postfix start.
4. No root required (nc uses regular TCP connect).

Report back: "Lab ready for Q009 — banners present on 21/22/25 of 192.168.52.129".
```
