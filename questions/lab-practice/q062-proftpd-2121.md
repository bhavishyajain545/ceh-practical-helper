# Q062 — ProFTPD on 2121

| Field | Value |
|---|---|
| **Target** | `192.168.52.129` / `192.168.52.130` |
| **Domain** | 05 — System Hacking |
| **Difficulty** | 🟡 Medium |
| **Tools** | `nc (netcat)` |
| **Time budget** | 15–25 min |

---

## 📝 Question

Identify version of ProFTPD on Metasploitable port 2121 and find related CVE for mod_copy.

---

## 🎯 Flag Format

```
proftpd_ver=<x.y.z>; cve=<CVE>
```

Example: `proftpd_ver=1.3.1; cve=CVE-2015-3306`

**⚠️ Format strict hai** — lowercase, no spaces around `=`, semicolon separator where shown.

---

## 💡 Hints

<details>
<summary>Hint 1 (click)</summary>

Banner on connect.
</details>

<details>
<summary>Hint 2</summary>

mod_copy CVE is 2015-3306 (later versions).
</details>

---

## ✅ Solution

<details>
<summary>Show solution</summary>

```bash
nc 192.168.52.129 2121
```

Banner reveals 1.3.1; CVE-2015-3306 applies to 1.3.5 — accept reference.

**Answer:** `proftpd_ver=1.3.1; cve=CVE-2015-3306`

📖 Ref: [playbooks/exploitation-playbook.md](../../playbooks/exploitation-playbook.md)
</details>

---

## 🤖 Claude Setup Prompt (for Claude-on-your-PC)

```
Pre-requisites for Q062:
1. Verify Metasploitable2 (192.168.52.129) up; proftpd listening on 2121: ssh msfadmin → sudo service proftpd status (start if down; apt has it installed on Metasploitable2).
2. From Parrot: nc 192.168.52.129 2121 should print ProFTPD banner.

Report back: "Lab ready for Q062 — ProFTPD 2121 reachable on 192.168.52.129".
```
