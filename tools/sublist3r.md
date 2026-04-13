# sublist3r — subdomain enumeration

> **Fast passive subdomain finder.** Queries Google, Yahoo, Bing, Baidu, Netcraft, VirusTotal, ThreatCrowd, DNSdumpster, crt.sh.

**Install check:** `sublist3r -h`

---

## 🎯 Cheat-flow

```bash
sublist3r -d target.com                       # basic
sublist3r -d target.com -v                    # verbose
sublist3r -d target.com -o subs.txt           # save
sublist3r -d target.com -t 50                 # 50 threads for bruteforce
sublist3r -d target.com -b                    # enable subbrute bruteforce
sublist3r -d target.com -p 80,443             # port-scan found subs
sublist3r -d target.com -e google,bing,crtsh  # pick engines
```

---

## 🔑 Flags

| Flag | Meaning |
|---|---|
| `-d <domain>` | Target domain (required) |
| `-b` | Enable subbrute bruteforce |
| `-t <N>` | Threads (default 30) |
| `-p <ports>` | Scan found hosts on these ports |
| `-v` | Verbose |
| `-e <engines>` | Comma list: google,yahoo,bing,baidu,ask,netcraft,dnsdumpster,virustotal,threatcrowd,ssl,passivedns |
| `-o <file>` | Output file |
| `-n` | No color |

---

## 📋 Recipes

```bash
# 1. Standard first-pass
sublist3r -d target.com -o subs.txt

# 2. Passive only, specific engines
sublist3r -d target.com -e crtsh,virustotal,dnsdumpster -o subs.txt

# 3. Passive + brute + port check
sublist3r -d target.com -b -p 80,443,8080 -o subs.txt

# 4. Feed to httpx for liveness
cat subs.txt | httpx -silent -status-code -title
```

---

## ⚠️ Gotchas

- Project is **lightly maintained** — for larger recon use `subfinder`, `amass`, or [recon-ng](recon-ng.md) CT module.
- Google/Bing quickly rate-limit — expect partial results.
- `-b` uses a small bruteforce wordlist; serve your own list for better coverage.
- Wildcard DNS domains will report thousands of junk hits — verify with `dig` or `httpx`.

---

## 🔗 Related

- [recon-ng](recon-ng.md) · [theharvester](theharvester.md) · [dnsrecon](dnsrecon.md) · [dig](dig.md)
