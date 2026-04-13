# 16 — IoT / OT

> IoT/OT questions hand you an IP of a camera, router, PLC, or printer and ask for the device type, default credentials, or a known CVE. Recon-heavy, exploitation-light.

## 🧭 Decision tree — "I see an IoT/OT question"

```
IP / device given?
│
├── "What kind of device is this"
│   ├── curl -I http://<IP>              → Server header (often "Boa", "GoAhead", "mini_httpd")
│   ├── nmap -sV -p- <IP>                → banners give it away
│   └── Shodan → https://shodan.io/host/<IP>
│
├── "Default credentials / login"
│   ├── Look up vendor+model in default-cred DB
│   ├── Try admin:admin, admin:password, root:root, admin:<blank>
│   └── hydra -L users.txt -P passes.txt <IP> http-get /
│
├── "Known CVEs for this device"
│   ├── searchsploit <vendor> <model>
│   └── Google `<firmware version> CVE`
│
├── "Industrial / SCADA (OT)"
│   ├── Modbus  TCP/502  → nmap --script modbus-discover -p 502 <IP>
│   ├── S7      TCP/102  → nmap --script s7-info -p 102 <IP>
│   ├── BACnet  UDP/47808 → nmap -sU --script bacnet-info -p 47808 <IP>
│   └── DNP3    TCP/20000
│
└── "Firmware image given"
    ├── binwalk -e firmware.bin        → extract filesystem
    ├── find ./ -name 'passwd' -o -name 'shadow'
    └── grep -r 'password\|token' squashfs-root/
```

## 📄 Files in this folder

- **[commands.md](commands.md)** — shodan / nmap / default-creds recipes
- **[walkthroughs.md](walkthroughs.md)** — full identify→access examples
- Question bank: **[../questions/by-domain/16-iot-ot.md](../questions/by-domain/16-iot-ot.md)**

## 🛠 Tools used in this domain

- **[shodan](../tools/shodan.md)** — device/banner search engine
- **[nmap](../tools/nmap.md)** — fingerprinting + NSE OT scripts
- **[hydra](../tools/hydra.md)** — credential brute force
- **[binwalk](../tools/binwalk.md)** — firmware extraction
- Default-cred lists: cirt.net/passwords, [SecLists/Passwords/Default-Credentials](https://github.com/danielmiessler/SecLists)

## ✅ Domain checklist

- [ ] Banner grab with curl + nmap and identify vendor
- [ ] Know the top 5 default creds (admin/admin, admin/password, root/root, admin/<blank>, support/support)
- [ ] Know Modbus=502, S7=102, BACnet=47808, DNP3=20000
- [ ] Run `nmap --script modbus-discover -p 502 <IP>`
- [ ] Extract a firmware image with binwalk
- [ ] Done all questions in [the question bank](../questions/by-domain/16-iot-ot.md)
