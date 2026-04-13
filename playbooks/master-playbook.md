# Master Playbook — "I got a question, where do I start?"

> Read the question twice. Then match the **keyword** below to the right domain/playbook.

| Keyword in question | Go here |
|---|---|
| "live hosts", "discover", "subnet", "ping sweep" | [Scanning playbook](scanning-playbook.md) |
| "open ports", "version", "OS", "scan" | [Scanning playbook](scanning-playbook.md) |
| "vulnerable", "CVE", "outdated" | [Scanning playbook](scanning-playbook.md) → vuln scripts |
| "share", "SMB", "NetBIOS" | [03-Enumeration](../03-enumeration/README.md) |
| "user enumeration", "SNMP", "LDAP" | [03-Enumeration](../03-enumeration/README.md) |
| "crack", "hash", "password", "NTLM", "shadow" | Hash-cracking playbook *(coming)* |
| "brute force login", "ssh login", "ftp login" | [05-System hacking → hydra](../05-system-hacking/README.md) |
| "SQL injection", "sqli", "dbms", "database name" | [13-SQLi](../13-sqli/README.md) |
| "directory", "admin panel", "robots.txt", "wordpress" | [12-Web apps](../12-web-apps/README.md) |
| "web server software", "Apache version", "IIS" | [11-Web servers](../11-web-servers/README.md) |
| "pcap", "capture", "wireshark", "packet" | [07-Sniffing](../07-sniffing/README.md) |
| "wifi", "WPA", "WEP", "handshake", "ESSID" | [14-Wireless](../14-wireless/README.md) |
| "stego", "hidden", "steghide", "exiftool", "image" | Crypto/stego playbook *(coming)* |
| "encrypted", "decrypt", "openssl", "RSA", "AES" | [18-Crypto](../18-cryptography/README.md) |
| "APK", "android", "manifest" | [15-Mobile](../15-mobile/README.md) |
| "S3", "bucket", "AWS" | [17-Cloud](../17-cloud/README.md) |
| "DoS", "flood", "syn flood" | [09-DoS](../09-dos/README.md) |
| "phishing", "credential harvester" | [08-Social engineering](../08-social-engineering/README.md) |

## Universal first moves

Before reading the question carefully:
1. Note the **target IP(s)** mentioned.
2. In a spare terminal, kick off a full nmap in background:
   ```bash
   nmap -sV -sC -p- -T4 -oA bg <IP>
   ```
3. Now read the question. By the time you understand what's asked, the scan is half-done.

→ Full first-5-commands list: [exam-day/first-5-commands.md](../exam-day/first-5-commands.md)
