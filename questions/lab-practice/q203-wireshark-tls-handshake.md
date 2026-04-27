# Q203 — Analyze TLS Handshake

| Field | Value |
|-------|-------|
| **Target** | local |
| **Domain** | Sniffing / Packet Analysis |
| **Difficulty** | 🟡 Medium |
| **Tools** | `wireshark, tshark` |
| **Time budget** | 8–12 min |

---

## 📝 Question

"Analyze `tls_traffic.pcap`. Find the TLS version and cipher suite negotiated in the handshake. Also find the server's certificate Common Name."

---

## 🎯 Flag Format

```
tls_version=<version>; cipher=<cipher_suite>; cn=<common_name>
```

Example: `tls_version=1.2; cipher=TLS_RSA_WITH_AES_128_CBC_SHA; cn=example.com`

---

## 💡 Hints

**Hint 1**

Filter: `tls.handshake` — Client Hello aur Server Hello mein version aur cipher milega.

**Hint 2**

`tshark -r tls_traffic.pcap -Y "tls.handshake.type==2" -T fields -e tls.handshake.version -e tls.handshake.ciphersuite`

---

## ✅ Solution

```bash
# TLS version and cipher
tshark -r tls_traffic.pcap -Y "tls.handshake.type==2" -T fields -e tls.handshake.version -e tls.handshake.ciphersuite
# Certificate CN
tshark -r tls_traffic.pcap -Y "tls.handshake.type==11" -T fields -e x509sat.uTF8String
```

**Answer:** `tls_version=1.2; cipher=<negotiated_cipher>; cn=localhost`

---

## 🤖 Claude Setup Prompt

```bash
tcpdump -i eth0 port 443 -w tls_traffic.pcap &
curl -k https://192.168.52.129/
sleep 3 && kill %1
```

Report back: "Lab ready for Q203 — tls_traffic.pcap with TLS handshake ready"
