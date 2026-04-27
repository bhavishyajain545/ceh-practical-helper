# Q210 — Multi-Step Attack Chain Analysis

| Field | Value |
|-------|-------|
| **Target** | local |
| **Domain** | Sniffing / Packet Analysis |
| **Difficulty** | 🔴 Hard |
| **Tools** | `wireshark, tshark` |
| **Time budget** | 15–20 min |

---

## 📝 Question

"Analyze `full_attack.pcap` which captures a complete attack. Identify: (1) Reconnaissance phase — what scan type was used, (2) Exploitation phase — what service was attacked, (3) Post-exploitation — what data was exfiltrated. Report each phase."

---

## 🎯 Flag Format

```
recon=<scan_type>; exploit=<service>; exfil=<data_type>
```

Example: `recon=syn_scan; exploit=ftp; exfil=passwords`

---

## 💡 Hints

**Hint 1**

Sort packets by time — early packets = recon, middle = exploit, late = exfil.

**Hint 2**

Check for: SYN flood (recon), specific service exploitation, then data transfer out.

---

## ✅ Solution

```bash
# Phase 1: Recon
tshark -r full_attack.pcap -Y "tcp.flags.syn==1 && tcp.flags.ack==0" | head -20
# Phase 2: Exploit
tshark -r full_attack.pcap -Y "ftp || ssh || http" -T fields -e frame.time -e ip.src -e ip.dst -e _ws.col.Protocol | head -20
# Phase 3: Exfil
tshark -r full_attack.pcap -Y "tcp.dstport > 1024 && data" | tail -20
```

**Answer:** `recon=syn_scan; exploit=ftp; exfil=credentials`

---

## 🤖 Claude Setup Prompt

```bash
tcpdump -i eth0 -w full_attack.pcap &
# Phase 1: Recon
nmap -sS 192.168.52.129 -p 21,22,80
# Phase 2: Exploit FTP
ftp -n 192.168.52.129 << FTP
user msfadmin msfadmin
get /etc/passwd
quit
FTP
sleep 5 && kill %1
```

Report back: "Lab ready for Q210 — full_attack.pcap with multi-phase attack ready"
