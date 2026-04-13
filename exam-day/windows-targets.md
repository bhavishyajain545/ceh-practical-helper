# Windows targets — common ports, defaults, gotchas

CEH Practical almost always gives you 1–3 Windows targets. Here's what to know.

## Common open ports

| Port | Service | First thing to try |
|---|---|---|
| 135 | MSRPC | `rpcclient -U "" -N <IP>` |
| 139 | NetBIOS | `nbtscan <IP>`, `nmblookup` |
| 445 | SMB | `enum4linux -a <IP>`, `smbclient -L //<IP>` |
| 3389 | RDP | `nmap -p 3389 --script rdp-enum-encryption <IP>` |
| 5985 | WinRM | `evil-winrm -i <IP> -u <user> -p <pass>` |
| 1433 | MSSQL | `nmap -p 1433 --script ms-sql-info <IP>` |
| 88 | Kerberos (DC!) | this is a Domain Controller — see below |

## Default / weak credentials to always try

| User | Password |
|---|---|
| Administrator | (blank) |
| Administrator | password |
| Administrator | admin |
| guest | (blank) |
| sa | (blank) — MSSQL |

## Identify Windows version fast

```bash
nmap -p 445 --script smb-os-discovery <IP>
# returns: OS, computer name, NetBIOS name, domain
```

## SMB attack tree

```
SMB open?
├── nmap --script "smb-vuln-*"   →  EternalBlue (MS17-010), MS08-067
├── enum4linux -a <IP>           →  users, shares, password policy
├── smbclient -L //<IP> -N       →  list shares anonymously
├── smbclient //<IP>/<share> -N  →  access share
├── smbmap -H <IP>               →  permissions
└── crackmapexec smb <IP> -u users.txt -p passwords.txt
```

## Domain Controller hints (port 88 + 445 + 389)

- AS-REP roast: `impacket-GetNPUsers <DOMAIN>/ -usersfile users.txt -no-pass`
- Kerberoast: `impacket-GetUserSPNs <DOMAIN>/<user>:<pass> -request`
- Dump hashes (with creds): `impacket-secretsdump <DOMAIN>/<user>:<pass>@<IP>`

## Common Windows file paths

| What | Where |
|---|---|
| SAM file | `C:\Windows\System32\config\SAM` |
| SYSTEM hive | `C:\Windows\System32\config\SYSTEM` |
| Hosts file | `C:\Windows\System32\drivers\etc\hosts` |
| IIS root | `C:\inetpub\wwwroot\` |
| User profile | `C:\Users\<user>\` |
| Scheduled tasks | `C:\Windows\System32\Tasks\` |
