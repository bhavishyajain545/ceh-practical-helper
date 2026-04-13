# loic — Low Orbit Ion Cannon

> **The "click to DDoS" GUI tool.** Sends TCP/UDP/HTTP floods. Historically associated with Anonymous.

**Launch (Windows / Mono):** `LOIC.exe` — or `mono LOIC.exe` on Linux.

---

## 🎯 Cheat-flow (GUI)

1. Enter URL or IP in **"Select your target"** → click **Lock on**.
2. Pick method: **TCP / UDP / HTTP**.
3. Port, threads, TCP/UDP message.
4. Click **IMMA CHARGIN MAH LAZER**.

"Hive Mind" / IRC control allowed a third party to coordinate thousands of clients — now disabled/not present in most forks.

---

## 🔑 Method notes

| Method | Behavior |
|---|---|
| **TCP** | Rapid SYN-open / payload flood |
| **UDP** | UDP packet flood (spoofing not supported) |
| **HTTP** | GET flood |

No proxying, no randomisation → **your real IP is in every packet**. People have been arrested using LOIC for exactly this reason.

---

## ⚠️ Gotchas

- **Non-anonymous** — do NOT run against anything without written authorisation.
- Trivially blocked by any modern network (stateful firewall, rate limit, CDN).
- JS fork (**JS LOIC**) runs in a browser tab and is the version you're likely to see in CEH theory.
- For CEH Practical you'll typically only be asked to *identify* LOIC, not run it.

---

## 🔗 Related

- [hulk](hulk.md) · [slowloris](slowloris.md) · [hping3](hping3.md)
