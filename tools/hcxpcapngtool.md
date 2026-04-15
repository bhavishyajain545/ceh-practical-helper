# hcxpcapngtool — pcapng → hashcat/john converter

> **The pcapng-to-hashcat bridge.** Takes the pcapng output from [hcxdumptool](hcxdumptool.md) (or a classic pcap) and converts WPA PMKIDs + EAPOL 4-way handshakes into the modern hashcat `-m 22000` format. Replaces the old `hccapx` flow (`cap2hccapx` + `-m 2500`).

**Install check (Parrot):** `hcxpcapngtool -v`
**Install if missing:** `sudo apt install hcxtools`

---

## 🎯 Cheat-flow

| Goal | Command |
|---|---|
| **pcapng → hashcat 22000** | `hcxpcapngtool -o hash.22000 capture.pcapng` |
| Filter only one ESSID | `hcxpcapngtool -o hash.22000 -E essid.txt capture.pcapng` |
| Extract all seen ESSIDs | `hcxpcapngtool -E essidlist.txt capture.pcapng` |
| Extract PMKIDs only (legacy 16800) | `hcxpcapngtool -z pmkid.16800 capture.pcapng` |
| Extract EAPOL only | `hcxpcapngtool -o hash.22000 --all capture.pcapng` |
| Convert an old `.cap` (pcap, not pcapng) | `hcxpcapngtool -o hash.22000 capture.cap` |

---

## 🔑 Flags

| Flag | Meaning |
|---|---|
| `-o <file>` | **Output — hashcat mode 22000** (combined PMKID + EAPOL) |
| `-z <file>` | Output legacy **16800** PMKID-only format |
| `-O <file>` | Plain word list of every string seen (great for custom dictionaries) |
| `-E <file>` | Dump ESSIDs seen (SSID recovery) |
| `-I <file>` | Dump identities (EAP) |
| `-U <file>` | Dump usernames from EAP |
| `-P <file>` | Dump PSKs if recovered from MIC |
| `-D <file>` | Dump MACs |
| `--all` | Include even non-replay-count-verified EAPOLs (more data, may include junk) |
| `--prefix=<name>` | Common prefix for all output files |
| `--essid-list=<file>` | Only process these ESSIDs |
| `--essid-max=<n>` | Max ESSID length filter |
| `-v` | Version |
| `-h` | Help |

---

## 🧪 22000 vs 16800 — which to use

| Mode | Format | Covers | When |
|---|---|---|---|
| **`-m 22000`** | unified `WPA*01*...*02*...` | PMKID **and** EAPOL 4-way | **Default — always use this** |
| `-m 16800` | legacy PMKID-only hash line | Only PMKID | Old tutorials; deprecated |
| `-m 2500` | `.hccapx` binary (legacy) | Only EAPOL 4-way | Deprecated — needs `cap2hccapx` |

Mode 22000 is the modern unified format — one file handles both PMKID and 4-way. Use it unless a very old hashcat build forces you to `16800`.

---

## 📋 Command recipes

```bash
# 1. Bread-and-butter — convert hcxdumptool output, crack with hashcat
hcxpcapngtool -o hash.22000 capture.pcapng
hashcat -m 22000 hash.22000 /usr/share/wordlists/rockyou.txt

# 2. SSID recovery from random capture (no cracking)
hcxpcapngtool -E essids.txt capture.pcapng
cat essids.txt

# 3. Target one specific SSID during conversion
echo "CorpWiFi" > want.txt
hcxpcapngtool -o corp.22000 --essid-list=want.txt capture.pcapng

# 4. Batch: many pcapngs into one hash file
hcxpcapngtool -o all.22000 *.pcapng

# 5. Legacy 16800 if needed
hcxpcapngtool -z pmkid.16800 capture.pcapng
hashcat -m 16800 pmkid.16800 rockyou.txt

# 6. Build a custom wordlist from capture strings
hcxpcapngtool -O wordlist.txt capture.pcapng
```

---

## 🎯 CEH exam patterns

**"Convert the capture file to a format hashcat can use":**
```bash
hcxpcapngtool -o hash.22000 capture.pcapng
```

**"What SSIDs were broadcast in the capture?":**
```bash
hcxpcapngtool -E essids.txt capture.pcapng && cat essids.txt
```

**End-to-end pipeline the exam rewards:**
```bash
sudo airmon-ng check kill && sudo airmon-ng start wlan0
sudo hcxdumptool -i wlan0mon -w cap.pcapng --enable_status=1
hcxpcapngtool -o hash.22000 cap.pcapng
hashcat -m 22000 hash.22000 /usr/share/wordlists/rockyou.txt
hashcat -m 22000 hash.22000 --show
```

---

## ⚠️ Gotchas

- **`-o` file is empty?** Your pcapng has no PMKID *and* no valid 4-way handshake. Recapture with longer duration or add `--all`.
- **"no hashes written"** is the most common error — means the EAPOL 4-way wasn't complete (M1/M2/M3/M4). Open in Wireshark, filter `eapol`, check you have all four messages.
- **Don't use `cap2hccapx` / `-m 2500` anymore.** The 22000 format superseded it in hashcat 6.x.
- **SSIDs containing unicode / NULL bytes** may be truncated — `-E` shows raw, check hex in Wireshark.
- **Tool name confusion:** `hcxpcaptool` (old, deprecated) vs `hcxpcapngtool` (current). Use the ng one.
- **Input must be pcapng or pcap.** Cuckoo-style dumps or Kismet `.pcapdump` may need conversion first.
- **PMKID may be present but hashcat still fails to crack** — that just means your wordlist doesn't have the PSK. Convert ≠ crack.

---

## 🔗 Related

- [hcxdumptool](hcxdumptool.md) — generates the pcapng input
- [hashcat](hashcat.md) — `-m 22000` cracks the output
- [aircrack-ng](aircrack-ng.md) — alternative offline cracker (accepts 22000 via newer builds)
- [wireshark](wireshark.md) — verify the EAPOL 4-way is complete (`eapol` filter)
- [14-wireless domain README](../14-wireless/README.md)
