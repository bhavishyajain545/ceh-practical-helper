# 16 IoT / OT — copy-paste commands

> Replace `<IP>` / `<VENDOR>` / `<MODEL>`.

## Identify the device

```bash
# HTTP banner
curl -I http://<IP>
curl -I https://<IP> -k

# nmap — version + OS
nmap -sV -O -p- <IP>

# Deep service probe
nmap -sV --version-all -p 80,443,23,22,21,161 <IP>
```

## Shodan (if you have internet + API key)

```bash
# CLI
shodan init <API_KEY>
shodan host <IP>
shodan search "vendor:<VENDOR> product:<MODEL>"
shodan search "modbus country:US"
shodan search "port:502"
shodan search "Server: Boa"
```

Or browse: `https://www.shodan.io/host/<IP>`

## Default credentials — try these first

```
admin:admin
admin:password
admin:1234
admin:<blank>
root:root
root:toor
root:admin
support:support
user:user
cisco:cisco
ubnt:ubnt         (Ubiquiti)
pi:raspberry      (Raspberry Pi)
```

Databases:
- https://cirt.net/passwords
- https://default-password.info
- `/usr/share/seclists/Passwords/Default-Credentials/`

## Brute force login

```bash
# HTTP basic auth
hydra -L users.txt -P /usr/share/seclists/Passwords/Default-Credentials/default-passwords.txt <IP> http-get /

# HTTP form (custom)
hydra -l admin -P passes.txt <IP> http-post-form "/login.php:user=^USER^&pass=^PASS^:Invalid"

# Telnet
hydra -l root -P passes.txt telnet://<IP>

# SSH
hydra -l root -P passes.txt ssh://<IP>
```

## OT / industrial protocols

```bash
# Modbus (TCP 502)
nmap --script modbus-discover -p 502 <IP>

# Siemens S7 (TCP 102)
nmap --script s7-info -p 102 <IP>

# BACnet (UDP 47808)
sudo nmap -sU --script bacnet-info -p 47808 <IP>

# EtherNet/IP (TCP/UDP 44818)
nmap --script enip-info -p 44818 <IP>

# DNP3 (TCP 20000)
nmap --script dnp3-info -p 20000 <IP>
```

## Vendor-specific quickies

```bash
# Printer (IPP 631 / JetDirect 9100)
nmap -p 631,9100 --script "pjl-ready-message,printer-info" <IP>

# UPnP (often leaks device info)
nmap -sU -p 1900 --script upnp-info <IP>

# SNMP — device name / model via public string
snmpwalk -v2c -c public <IP>
onesixtyone <IP> /usr/share/seclists/Discovery/SNMP/common-snmp-community-strings.txt
```

## Firmware extraction

```bash
# Look at the file
file firmware.bin
binwalk firmware.bin

# Extract (carves out embedded files + filesystem)
binwalk -e firmware.bin
cd _firmware.bin.extracted/

# Typical finds
find . -name 'passwd' -o -name 'shadow' -o -name '*.conf'
grep -r 'password\|token\|api' squashfs-root/ 2>/dev/null
```

## Known CVE lookup

```bash
searchsploit <VENDOR> <MODEL>
searchsploit dlink dir-645
```
