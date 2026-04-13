# smtp-user-enum — SMTP user enumeration via VRFY / EXPN / RCPT

> **Guess valid usernames on SMTP servers.** Abuses `VRFY`, `EXPN`, and `RCPT TO:` commands.

**Install check:** `smtp-user-enum -h`

---

## 🎯 Cheat-flow

```bash
smtp-user-enum -M VRFY -U users.txt -t 10.10.10.5
smtp-user-enum -M EXPN -U users.txt -t 10.10.10.5
smtp-user-enum -M RCPT -U users.txt -t 10.10.10.5 -f from@example.com
smtp-user-enum -M VRFY -U users.txt -T hosts.txt          # many hosts
smtp-user-enum -M VRFY -u root -t 10.10.10.5              # single user
smtp-user-enum -M VRFY -U users.txt -t 10.10.10.5 -p 587  # submission port
```

---

## 🔑 Flags

| Flag | Meaning |
|---|---|
| `-M <VRFY|EXPN|RCPT>` | Method |
| `-u <user>` | Single user |
| `-U <file>` | User file |
| `-t <host>` | Single target |
| `-T <file>` | Host file |
| `-p <port>` | Port (default 25) |
| `-f <addr>` | MAIL FROM (required for RCPT) |
| `-D <domain>` | Append `@domain` to usernames |
| `-m <N>` | Max probes per connection |
| `-d` | Debug |

---

## 🧠 Method differences

| Method | Command | Notes |
|---|---|---|
| **VRFY** | `VRFY user` | Simplest. Often disabled. |
| **EXPN** | `EXPN alias` | Expands mailing lists. Often disabled. |
| **RCPT TO** | `MAIL FROM:<f>` then `RCPT TO:<u>` | Works even when VRFY/EXPN are off — needs `-f`. |

---

## 📋 Recipe

```bash
# 1. Confirm the server banner & which verbs are allowed
nc 10.10.10.5 25
220 mail.target.com ESMTP Postfix
HELP
EHLO test

# 2. If VRFY works:
smtp-user-enum -M VRFY -U /usr/share/seclists/Usernames/top-usernames-shortlist.txt -t 10.10.10.5

# 3. RCPT fallback
smtp-user-enum -M RCPT -U users.txt -t 10.10.10.5 -f 'test@example.com'
```

Typical valid response:
```
10.10.10.5: root exists
10.10.10.5: admin exists
```

---

## ⚠️ Gotchas

- Modern Postfix/Exim disable VRFY/EXPN by default.
- Some servers return **same response** for valid/invalid usernames → method useless, try another.
- Rate limiting / greylisting may kick in → slow down / use `-m 1`.
- CEH Practical often asks the count of valid users — save output with `-o`.

---

## 🔗 Related

- [nmap](nmap.md) `--script smtp-enum-users` · [hydra](hydra.md)
