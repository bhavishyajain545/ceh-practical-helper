# Q008 — NSE Vulners Script

| Field | Value |
|---|---|
| **Target** | `192.168.52.129` (Metasploitable 2) |
| **Domain** | 02 — Scanning |
| **Difficulty** | 🟢 Easy |
| **Tools** | `nmap`, `hping3`, `masscan`, `nc` |
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
1. Metasploitable vsftpd running on 21.

Report back: "Lab ready for Q008".
```
