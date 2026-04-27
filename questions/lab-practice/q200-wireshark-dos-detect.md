# Q200 — Identify DoS/Flood Attack in PCAP

| Field | Value |
|-------|-------|
| **Target** | local |
| **Domain** | Sniffing / Packet Analysis |
| **Difficulty** | 🟡 Medium |
| **Tools** | `wireshark, tshark` |
| **Time budget** | 8–12 min |

---

## 📝 Question

"Analyze `dos_attack.pcap`. Identify the type of DoS attack (SYN flood, ICMP flood, etc.), the attacker IP, and the target IP. Report the attack type and packet count."

---

## 🎯 Flag Format

```
attack=<type>; attacker=<ip>; target=<ip>; packets=<number>
```

Example: `attack=syn_flood; attacker=10.0.0.5; target=192.168.1.1; packets=5000`

---

## 💡 Hints

**Hint 1**

Statistics → Conversations mein dekho — ek IP se bahut zyada traffic = DoS.

**Hint 2**

`tshark -r dos_attack.pcap -q -z conv,ip` — top talkers dikhenge.

---

## ✅ Solution

```bash
# Identify top source
tshark -r dos_attack.pcap -T fields -e ip.src | sort | uniq -c | sort -rn | head -3
# Check if SYN flood
tshark -r dos_attack.pcap -Y "tcp.flags.syn==1 && tcp.flags.ack==0" | wc -l
# Check if ICMP flood
tshark -r dos_attack.pcap -Y "icmp" | wc -l
```

**Answer:** `attack=syn_flood; attacker=192.168.52.128; target=192.168.52.129; packets=5000`

---

## 🤖 Claude Setup Prompt

```bash
tcpdump -i eth0 -w dos_attack.pcap &
hping3 -S --flood -p 80 192.168.52.129 &
sleep 5 && kill %2 && sleep 1 && kill %1
```

Report back: "Lab ready for Q200 — dos_attack.pcap with flood attack traffic ready"
