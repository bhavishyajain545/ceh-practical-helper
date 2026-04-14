# Q008 — NSE Vulners Script

| Field | Value |
|---|---|
| **Target** | `192.168.52.129` (Metasploitable 2) |
| **Domain** | 02 — Scanning |
| **Difficulty** | 🟢 Easy |
| **Tools** | `nmap` |
| **Time budget** | 5–10 min |

---

## 📝 Question

Run nmap with `--script vuln` against Metasploitable port 21 and identify the **CVE** for vsftpd 2.3.4 backdoor.

---

## 🎯 Flag Format

```
cve=<CVE-YYYY-NNNN>
```

Example: `cve=CVE-2011-2523`

**⚠️ Format strict hai** — lowercase, no spaces around `=`, semicolon separator where shown.

---

## 💡 Hints

<details>
<summary>Hint 1 (click)</summary>

`nmap --script vuln -p 21 <ip>`
</details>

<details>
<summary>Hint 2</summary>

vsftpd 2.3.4 backdoor CVE is from 2011.
</details>

---

## ✅ Solution

<details>
<summary>Show solution</summary>

```bash
nmap --script vuln -p 21 -sV 192.168.52.129
```

Script flags `vsftpd 2.3.4 backdoor` → CVE-2011-2523.

**Answer:** `cve=CVE-2011-2523`

📖 Ref: [playbooks/scanning-playbook.md](../../playbooks/scanning-playbook.md)
</details>

---

## 🤖 Claude Setup Prompt (for Claude-on-your-PC)

```
Pre-requisites for Q008:
1. Verify Metasploitable2 (192.168.52.129) is reachable: ping -c 2 192.168.52.129.
2. vsftpd 2.3.4 must be running on port 21 — that banner is what vuln NSE scripts match against:
   - Confirm: nc 192.168.52.129 21 → "220 (vsFTPd 2.3.4)".
   - If down: ssh msfadmin@192.168.52.129 → sudo service vsftpd start.
3. vuln NSE category scripts ship with nmap — verify: ls /usr/share/nmap/scripts/ftp-vsftpd-backdoor.nse. If absent, sudo apt install -y nmap.
4. No internet required (offline NSE db is local); no root needed for -sV --script vuln against a single port.

Report back: "Lab ready for Q008 — vsftpd 2.3.4 banner confirmed on 192.168.52.129:21".
```
