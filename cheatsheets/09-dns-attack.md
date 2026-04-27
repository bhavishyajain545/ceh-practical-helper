# 🔓 DNS PORT OPEN (53) — Ab Kya?

---

## CASE 1: Zone Transfer Try Karo (Sabse Pehle)
```bash
dig axfr @<IP> <domain>
dig axfr @<IP> ceh-lab.local
host -t axfr <domain> <IP>
dnsrecon -d <domain> -n <IP> -t axfr
nmap --script dns-zone-transfer -p 53 --script-args dns-zone-transfer.domain=<domain> <IP>
```
> Zone transfer success? → Saare subdomains, IPs, records mil jayenge

---

## CASE 2: DNS Enumeration
```bash
nslookup
> server <IP>
> set type=any
> <domain>

dig @<IP> <domain> ANY
dnsrecon -d <domain> -n <IP>
```

---

## CASE 3: Reverse DNS Lookup
```bash
dig @<IP> -x <TARGET_IP>
nmap --script dns-brute --script-args dns-brute.domain=<domain> <IP>
```

---

## CASE 4: DNS Subdomain Brute Force
```bash
dnsrecon -d <domain> -D /usr/share/wordlists/seclists/Discovery/DNS/subdomains-top1million-5000.txt -t brt
fierce --domain <domain> --dns-servers <IP>
```

---

## KEY POINT:
> DNS question = mostly **zone transfer** ya **record enumeration**
> Domain name nahi pata? `nmap -sV -p 53 <IP>` se ya `/etc/hosts` check karo

---

## QUICK DECISION:
```
DNS open (53)
  ├─ Zone transfer: dig axfr @<IP> <domain>
  ├─ Records: dig @<IP> <domain> ANY
  ├─ Reverse lookup: dig -x <IP>
  └─ Subdomain brute: dnsrecon -d <domain> -t brt
```
