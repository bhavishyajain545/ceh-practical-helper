# Q062 — ProFTPD on 2121

| Field | Value |
|---|---|
| **Target** | `192.168.52.129` / `192.168.52.130` |
| **Domain** | 05 — System Hacking |
| **Difficulty** | 🟡 Medium |
| **Tools** | `metasploit`, `hydra`, `john`, `hashcat`, `linpeas`, `mimikatz` |
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
1. Verify VMs running: ping 192.168.52.129 and ping 192.168.52.130 from 192.168.52.128.
2. Required services for this Q already up by default on the relevant target.
3. If something is down, restart it on the target VM.

Report back: "Lab ready for Q062".
```
