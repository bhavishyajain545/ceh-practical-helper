# Q190 — Inspect SSL Certificate Using OpenSSL

| Field | Value |
|-------|-------|
| **Target** | `192.168.52.129` |
| **Domain** | Cryptography |
| **Difficulty** | 🟡 Medium |
| **Tools** | `openssl` |
| **Time budget** | 5–10 min |

---

## 📝 Question

"Connect to `192.168.52.129:443` and inspect the SSL certificate. Report the Common Name (CN), issuer, and expiry date."

---

## 🎯 Flag Format

```
cn=<common_name>; issuer=<issuer>; expiry=<date>
```

Example: `cn=localhost; issuer=Self-Signed; expiry=2025-12-31`

---

## 💡 Hints

**Hint 1**

`openssl s_client -connect host:port` se certificate details milenge.

**Hint 2**

`openssl s_client -connect 192.168.52.129:443 | openssl x509 -noout -subject -issuer -dates`

---

## ✅ Solution

```bash
echo | openssl s_client -connect 192.168.52.129:443 2>/dev/null | openssl x509 -noout -subject -issuer -dates
```

**Answer:** `cn=localhost; issuer=localhost; expiry=<date_from_cert>`

---

## 🤖 Claude Setup Prompt

1. Ensure Metasploitable2 is running with HTTPS enabled.
2. Verify port 443 is open: `nmap -p 443 192.168.52.129`

Report back: "Lab ready for Q190 — HTTPS service on 192.168.52.129:443 accessible"
