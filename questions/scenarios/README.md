# Scenario Question Bank — Multi-Domain Chains

> Real CEH Practical questions rarely stay in one domain. Each scenario here is a full kill chain: scan → enum → exploit → loot → answer. Use them to rehearse the *flow*, not just the tools.

**Legend:** 🟢 easy · 🟡 medium · 🔴 hard

## Index

| # | Scenario | Difficulty | Domains crossed | ~Time |
|---|----------|------------|-----------------|-------|
| 01 | [SMB EternalBlue to Domain Admin](scenario-01-smb-eternalblue-to-domain-admin.md) | 🟡 | 02, 03, 04, 05, 18 | 25 min |
| 02 | [FTP Anon to SSH Credential Reuse](scenario-02-ftp-anon-to-ssh-reuse.md) | 🟢 | 02, 03, 05 | 15 min |
| 03 | [Web LFI to SSH Private Key](scenario-03-lfi-to-ssh-key.md) | 🟡 | 02, 12, 05 | 25 min |
| 04 | [WordPress Plugin RCE to Root](scenario-04-wordpress-plugin-rce-to-root.md) | 🔴 | 02, 12, 05 | 35 min |
| 05 | [SQLi to Admin Hash to Portal Login](scenario-05-sqli-to-admin-login.md) | 🟡 | 02, 13, 18, 12 | 25 min |
| 06 | [SNMP Enum to Hydra SSH Brute](scenario-06-snmp-to-hydra-ssh.md) | 🟡 | 02, 03, 05 | 20 min |
| 07 | [Tomcat Default Creds to WAR Shell](scenario-07-tomcat-default-creds-war-shell.md) | 🟡 | 02, 11, 05 | 20 min |
| 08 | [Anonymous SMB Share to MSSQL](scenario-08-smb-share-to-mssql.md) | 🟡 | 02, 03, 05 | 25 min |
| 09 | [Steganography to VeraCrypt Container](scenario-09-steg-to-veracrypt.md) | 🟡 | 18, 06 | 20 min |
| 10 | [PCAP Cred Extraction to Service Reuse](scenario-10-pcap-http-cred-reuse.md) | 🟡 | 07, 05 | 20 min |
| 11 | [WPA2 Handshake Capture and Crack](scenario-11-wpa2-handshake-crack.md) | 🟡 | 14, 18 | 20 min |
| 12 | [APK Reverse to Hardcoded API Key](scenario-12-apk-hardcoded-api-key.md) | 🟡 | 15, 12 | 25 min |
| 13 | [AS-REP Roast to Kerberoast to DA](scenario-13-asreproast-to-kerberoast.md) | 🔴 | 03, 05, 18 | 40 min |
| 14 | [Open Redis to SSH Key Write to Root](scenario-14-redis-ssh-key-write.md) | 🟡 | 02, 03, 05 | 20 min |
| 15 | [JWT None-Alg to Admin Panel](scenario-15-jwt-none-alg-forgery.md) | 🟡 | 12, 18 | 25 min |

## How to use during the exam

1. **Skim the table.** When a question lands, identify the *entry point* (SMB? Web? PCAP?) and jump to the matching scenario.
2. **Each scenario has a "Variant questions" section** — these map small exam questions to a subset of steps. You don't always need the full chain.
3. **"Gotchas across this chain"** captures the 3–5 things that eat exam time. Read them before running commands.
4. Links to tool cheatsheets open in adjacent tabs — keep this file pinned in one tab, tools in another.

## Cross-references

- [Per-domain question banks](../by-domain/) — focused single-topic drills.
- [Full mock exams](../full-mock-exams/) — timed combined runs.
- [Tool index](../../tools/) — every tool referenced here.
- [Scanning playbook](../../playbooks/scanning-playbook.md) — decision tree for step 1 of any scenario.

## Coverage map — which tools each scenario exercises

| Tool | Scenarios |
|------|-----------|
| nmap | 01, 02, 03, 04, 05, 06, 07, 08, 13, 14, 15 |
| metasploit | 01, 04, 07 |
| hashcat | 01, 05, 11, 13, 15 |
| impacket | 08, 13 |
| hydra | 06, 07 |
| sqlmap | 05 |
| wpscan | 04 |
| smbclient / smbmap | 08 |
| snmpwalk | 06 |
| wireshark / tcpdump | 10 |
| aircrack-ng | 11 |
| steghide / binwalk / exiftool | 09 |
| crackmapexec / evil-winrm | 13 |
| enum4linux | 01 |
| ffuf | 03 |
| msfvenom | 07 |
| john | 03 |
| hashid | 01, 05 |
