# Q057 — sudo -l Misconfig

| Field | Value |
|---|---|
| **Target** | `192.168.52.129` / `192.168.52.130` |
| **Domain** | 05 — System Hacking |
| **Difficulty** | 🔴 Hard |
| **Tools** | `sudo` |
| **Time budget** | 15–25 min |

---

## 📝 Question

As `msfadmin` on Metasploitable, run `sudo -l`. What does it show?

---

## 🎯 Flag Format

```
sudo_perm=<rule>
```

Example: `sudo_perm=ALL=(ALL) ALL`

**⚠️ Format strict hai** — lowercase, no spaces around `=`, semicolon separator where shown.

---

## 💡 Hints

<details>
<summary>Hint 1 (click)</summary>

`sudo -l` requires password (msfadmin).
</details>

<details>
<summary>Hint 2</summary>

Default msfadmin can run anything via sudo.
</details>

---

## ✅ Solution

<details>
<summary>Show solution</summary>

```bash
sudo -l
```

msfadmin may run ALL commands as ALL users.

**Answer:** `sudo_perm=ALL=(ALL) ALL`

📖 Ref: [playbooks/system-hacking-playbook.md](../../playbooks/system-hacking-playbook.md)
</details>

---

## 🤖 Claude Setup Prompt (for Claude-on-your-PC)

```
Pre-requisites for Q057:
1. SSH as msfadmin (msfadmin/msfadmin) to 192.168.52.129.
2. msfadmin has default NOPASSWD-like sudoers (actually 'ALL=(ALL) ALL' requiring password).
3. No extra setup.

Report back: "Lab ready for Q057 — msfadmin ssh access to 192.168.52.129".
```
