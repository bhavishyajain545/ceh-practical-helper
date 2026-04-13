# Question Bank — IoT / OT (8 questions)

> IoT and ICS/SCADA recon. CEH here is mostly "fingerprint the weird port, find the default creds".

**Legend:** 🟢 easy · 🟡 medium · 🔴 hard

---

### Q1 🟢 — Identify the IoT device on 10.10.10.90 via banner grab

**Category:** Banner grab | **Tools:** [nmap](../../tools/nmap.md), `curl`

**Steps:**
1. `nmap -sV -p- 10.10.10.90` ← [why -sV](../../tools/nmap.md#scan-techniques)
2. For HTTP UIs: `curl -sI http://10.10.10.90/` — read `Server:` and `WWW-Authenticate:` realm.
3. Check TELNET/SSH banners too: `nc 10.10.10.90 23`.

**Answer format:** vendor + model (e.g. `Hikvision DS-2CD2042WD`).

**Gotcha:** the Basic-Auth realm in `WWW-Authenticate` often leaks the model name — always check it.

---

### Q2 🟢 — Find default credentials for the device model from Q1

**Category:** Default creds | **Tools:** [default cred lists](../../tools/default-creds.md), browser

**Steps:**
1. Check local list: `grep -i "hikvision" /usr/share/seclists/Passwords/Default-Credentials/*.csv`
2. Or search the DefaultCreds-cheat-sheet repo.
3. Try the common ones: `admin:admin`, `admin:12345`, `root:root`, `admin:<blank>`.

**Answer format:** `user:pass`

**Gotcha:** CEH uses well-known vendors (Hikvision=`admin:12345`, Dahua=`admin:admin`, D-Link=`admin:<blank>`, MikroTik=`admin:<blank>`).

---

### Q3 🟡 — Is Modbus running on 10.10.10.100?

**Category:** ICS recon | **Tools:** [nmap NSE](../../tools/nmap.md#nse-scripts-the-money-section)

**Steps:**
1. `nmap -p 502 -sV 10.10.10.100` ← Modbus TCP = port 502
2. `nmap -p 502 --script modbus-discover 10.10.10.100`

**Answer format:** Yes/No + slave ID if printed.

**Gotcha:** Modbus has no auth by design — if 502 is open you can usually read coils/registers directly.

---

### Q4 🟡 — Enumerate slave IDs on the Modbus device at 10.10.10.100

**Category:** ICS enum | **Tools:** [nmap NSE](../../tools/nmap.md#nse-scripts-the-money-section)

**Steps:**
1. `nmap -p 502 --script modbus-discover --script-args='modbus-discover.aggressive=true' 10.10.10.100`
2. Read the `Positive response from slave ID X` lines.

**Answer format:** slave ID number, or vendor info (e.g. `Schneider Electric`).

**Gotcha:** aggressive mode walks all 247 possible slave IDs — it's slow, let it finish.

---

### Q5 🟡 — Identify the SCADA protocol on port 102 / 20000 / 44818

**Category:** Protocol ID | **Tools:** [nmap](../../tools/nmap.md)

**Steps:**
1. `nmap -sV -p 102,502,20000,44818,47808 10.10.10.110`
2. Match port -> protocol:
   - 102 = Siemens S7 (script: `s7-info`)
   - 502 = Modbus
   - 20000 = DNP3 (script: `dnp3-info`)
   - 44818 = EtherNet/IP (script: `enip-info`)
   - 47808 = BACnet (script: `bacnet-info`)
3. Run the matching NSE script.

**Answer format:** protocol name (e.g. `Siemens S7`, `DNP3`).

---

### Q6 🟡 — Find the firmware version of the S7 PLC at 10.10.10.110

**Category:** Firmware recon | **Tools:** [nmap NSE](../../tools/nmap.md#nse-scripts-the-money-section)

**Steps:**
1. `nmap -p 102 --script s7-info 10.10.10.110`
2. Read the `Firmware:` and `Module:` lines in output.

**Answer format:** version string (e.g. `V.3.2.12`)

**Gotcha:** S7 also prints the plant ID / serial — CEH sometimes asks for those instead of version. Copy the whole block.

---

### Q7 🔴 — Search Shodan for the exposed device fingerprint

**Category:** OSINT | **Tools:** [shodan CLI](../../tools/shodan.md)

**Steps:**
1. `shodan init <API_KEY>`
2. Query by port + product:
   ```bash
   shodan search 'port:502 country:US'
   shodan search 'product:"Siemens SIMATIC"'
   shodan host 10.10.10.110
   ```
3. For IoT cams: `shodan search 'Server: Hikvision'`.

**Answer format:** count, or an IP/banner string.

**Gotcha:** exam usually gives you an offline Shodan export — `cat shodan.json | jq '.ip_str, .product'`.

---

### Q8 🔴 — Exploit the default web admin on the IoT device at 10.10.10.90

**Category:** Exploitation | **Tools:** `curl`, browser, [default creds](../../tools/default-creds.md)

**Steps:**
1. Browse to `http://10.10.10.90/`, note the login form.
2. Try default creds from Q2.
3. Once in, look for the flag/answer: often "Device Info" or "System" pages print the serial/firmware/MAC the question asks for.
4. Many IoT UIs have an unauthenticated info endpoint:
   ```bash
   curl -s http://10.10.10.90/system.ini
   curl -s http://10.10.10.90/cgi-bin/ViewLog.asp
   curl -s http://10.10.10.90/device.rsp
   ```

**Answer format:** whatever the admin panel reveals (MAC, serial, firmware, user list).

**Gotcha:** don't waste time on CSRF/cookies — for CEH IoT, the page you need is almost always a GET away once logged in.

---

## 📌 Quick links

- [nmap reference (NSE scripts)](../../tools/nmap.md#nse-scripts-the-money-section)
- [default credentials cheat sheet](../../tools/default-creds.md)
- [shodan CLI reference](../../tools/shodan.md)
- [16-iot-ot README](../../16-iot-ot/README.md)
