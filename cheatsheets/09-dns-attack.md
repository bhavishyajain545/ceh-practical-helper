# DNS PORT OPEN (53) — Ab Kya Karu?

---

## Pehle Samjho: DNS Kya Hai?

DNS = **Domain Name System**. Ye internet ka phonebook hai.
Jaise tumhare phone mein "Mummy" save hai aur peeche number hota hai — waise hi DNS mein
`google.com` ka naam save hai aur peeche IP address `142.250.x.x` hota hai.

**Key Facts:**
- Port: **53 (TCP aur UDP dono)**
- UDP 53 = normal DNS queries (naam se IP dhundna)
- TCP 53 = **zone transfers** (ye important hai attack ke liye!)
- DNS server pe **zone file** hoti hai jisme domain ke SAARE records hote hain
- CEH exam mein DNS = **Zone Transfer try karo** (90% yahi poochte hain)

### DNS Record Types (Ye Jaano):
| Record | Kya hai                           | Example                          |
|--------|-----------------------------------|----------------------------------|
| A      | Domain ka IPv4 address            | google.com → 142.250.1.1        |
| AAAA   | Domain ka IPv6 address            | google.com → 2607:f8b0::        |
| MX     | Mail server kahan hai             | google.com → mail.google.com    |
| NS     | Nameserver kaun hai               | google.com → ns1.google.com     |
| CNAME  | Alias (doosra naam)               | www.google.com → google.com     |
| TXT    | Text info (SPF, verification)     | google.com → "v=spf1 ..."       |
| SOA    | Zone ka authority info            | Primary NS, admin email          |
| PTR    | Reverse lookup (IP se naam)       | 1.1.250.142 → google.com        |

### Zone Transfer Kya Hai Aur Khatarnaak Kyun Hai?

**Zone Transfer (AXFR)** = DNS server se poori zone file maangna — yaani domain ke
SAARE subdomains, SAARE IP addresses, SAARE records ek saath.

**Normally:** Ye sirf authorized DNS servers ke beech hota hai (primary → secondary sync ke liye).
**Attack mein:** Agar DNS server misconfigured hai toh koi bhi zone transfer request kar sakta hai!

**Kya milta hai zone transfer se:**
- Saare subdomains (admin.target.com, internal.target.com, vpn.target.com)
- Internal IP addresses
- Mail servers
- Hidden services

**Isliye ye goldmine hai!** Ek command se poora network map mil jaata hai.

---

## STEP 1: Domain Name Dhundo (Agar Pata Nahi)

Zone transfer ke liye domain name chahiye. Agar nahi pata:

```bash
# Nmap se version info mein domain dikh sakta hai
nmap -sV -p 53 <IP>

# Target machine ka hostname check karo
nslookup
> server <IP>
> 127.0.0.1
# Response mein domain name dikh sakta hai

# Reverse lookup se domain dhundo
dig @<IP> -x <IP>

# /etc/hosts file check karo (lab mein aksar yahan hota hai)
cat /etc/hosts
```

**Common exam domains:** `ceh-lab.local`, `certifiedhacker.com`, `target.com`, `example.com`

---

## STEP 2: Zone Transfer Try Karo (SABSE PEHLE!)

Ye pehla command hona chahiye jab DNS port open dikhe:

```bash
# dig se zone transfer (BEST method)
dig axfr @<IP> <domain>
dig axfr @<IP> ceh-lab.local

# host command se
host -t axfr <domain> <IP>
host -t axfr ceh-lab.local <IP>

# dnsrecon se (automated tool)
dnsrecon -d <domain> -n <IP> -t axfr

# nmap script se
nmap --script dns-zone-transfer -p 53 --script-args dns-zone-transfer.domain=<domain> <IP>
```

**`dig axfr` explained:**
- `dig` = DNS query tool
- `axfr` = zone transfer request type
- `@<IP>` = kis DNS server se poochna hai
- `<domain>` = kis domain ka transfer chahiye

**Output mein kya dekho (SUCCESS):**
```
ceh-lab.local.    IN    A       10.10.1.1
admin.ceh-lab.local.  IN  A    10.10.1.5
mail.ceh-lab.local.   IN  MX   10.10.1.10
vpn.ceh-lab.local.    IN  A    10.10.1.20
```
Ye sab subdomains aur unke IPs hain — JACKPOT!

**Output mein kya dekho (FAIL):**
```
; Transfer failed.
```
Matlab zone transfer blocked hai — aage ke steps try karo.

---

## STEP 3: DNS Records Enumerate Karo

Zone transfer nahi hua? Individual records nikalo:

### Method 1: nslookup (Interactive)
```bash
nslookup
> server <IP>          # DNS server set karo
> set type=any         # saare record types chahiye
> <domain>             # domain enter karo
> set type=mx          # sirf mail servers
> <domain>
> set type=ns          # sirf nameservers
> <domain>
> set type=txt         # TXT records
> <domain>
> exit
```

### Method 2: dig (One-liner)
```bash
# Saare records
dig @<IP> <domain> ANY

# Specific record types
dig @<IP> <domain> A       # IPv4 addresses
dig @<IP> <domain> MX      # Mail servers
dig @<IP> <domain> NS      # Name servers
dig @<IP> <domain> TXT     # Text records
dig @<IP> <domain> SOA     # Authority info
dig @<IP> <domain> AAAA    # IPv6 addresses
```

### Method 3: dnsrecon (Automated)
```bash
dnsrecon -d <domain> -n <IP>
```

**Output mein kya dekho:**
- `ANSWER SECTION` ke under records aayenge
- Mail server ka address, nameserver info, TXT records mein sensitive info ho sakti hai

---

## STEP 4: Reverse DNS Lookup

Agar tumhe IP pata hai lekin domain nahi:

```bash
# Single IP ka reverse lookup
dig @<IP> -x <TARGET_IP>

# nslookup se
nslookup <TARGET_IP> <DNS_SERVER_IP>

# Poore subnet ka reverse lookup
dnsrecon -r 10.10.1.0/24 -n <IP>
```

**WHY reverse lookup:** Kabhi kabhi question mein IP diya hota hai aur poochte hain "ye IP kiska hai?" — reverse DNS se domain mil jaata hai.

**Output mein kya dekho:**
```
1.1.10.10.in-addr.arpa.  PTR   server.ceh-lab.local.
```
Matlab IP 10.10.1.1 ka naam "server.ceh-lab.local" hai.

---

## STEP 5: Subdomain Brute Force

Zone transfer fail hua aur records se zyada kuch nahi mila? Subdomain guessing karo:

```bash
# dnsrecon se brute force
dnsrecon -d <domain> -D /usr/share/wordlists/seclists/Discovery/DNS/subdomains-top1million-5000.txt -t brt -n <IP>

# fierce se
fierce --domain <domain> --dns-servers <IP>

# nmap script se
nmap --script dns-brute --script-args dns-brute.domain=<domain> <IP>

# gobuster DNS mode
gobuster dns -d <domain> -w /usr/share/wordlists/seclists/Discovery/DNS/subdomains-top1million-5000.txt -r <IP>:53
```

**Flags explained:**
- `-D wordlist.txt` = subdomain names ki list
- `-t brt` = brute force mode
- `-n <IP>` = DNS server specify karo

**Output mein kya dekho:**
```
[+] admin.ceh-lab.local - 10.10.1.5
[+] ftp.ceh-lab.local - 10.10.1.8
```
Naye subdomains mile! Ab in IPs ko scan karo.

---

## Common Mistakes (Mat Karna Ye)

1. **Zone transfer mein domain name nahi diya** — `dig axfr @<IP>` NAHI chalega. Domain name ZAROORI hai: `dig axfr @<IP> domain.com`

2. **Domain name pata nahi hai** — `/etc/hosts` check karo, `nmap -sV` karo, reverse lookup try karo. Lab mein aksar hosts file mein hota hai.

3. **dig command nahi mil raha** — Kali mein hota hai. Agar nahi hai: `apt install dnsutils`

4. **Zone transfer fail hua toh ruk gaye** — Zone transfer sirf EK option hai. Records enumerate karo, subdomain brute force karo, reverse lookup karo.

5. **TCP vs UDP confuse** — Normal DNS query = UDP. Zone transfer = TCP. Dono port 53 pe hote hain.

6. **`ANY` query mein kuch nahi aaya** — Kuch DNS servers `ANY` query block karte hain. Individual record types try karo (A, MX, NS, TXT separately).

---

## Quick Decision Tree

```
Port 53 Open (DNS) hai?
|
+-- Domain name pata hai?
|   |
|   +-- NAHI --> /etc/hosts check karo
|   |           nmap -sV -p 53 <IP>
|   |           dig @<IP> -x <IP> (reverse lookup)
|   |
|   +-- HAAN --> Aage badho
|
+-- ZONE TRANSFER TRY KARO (pehle ye!)
|   dig axfr @<IP> <domain>
|   |
|   +-- SUCCESS --> Saare subdomains + IPs mil gaye!
|   |               In IPs ko nmap se scan karo
|   |
|   +-- FAIL --> Aage badho
|
+-- DNS Records Enumerate Karo
|   dig @<IP> <domain> ANY
|   dig @<IP> <domain> MX
|   dig @<IP> <domain> NS
|   |
|   +-- Records mile --> Note karo mail servers, nameservers
|
+-- Subdomain Brute Force
|   dnsrecon -d <domain> -D wordlist.txt -t brt -n <IP>
|   |
|   +-- Naye subdomains mile --> In IPs ko scan karo
|
+-- Reverse DNS Lookup (IP se domain dhundo)
    dnsrecon -r 10.10.1.0/24 -n <IP>
```
