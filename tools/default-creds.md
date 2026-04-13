# default-creds — common default credentials cheat-sheet

> **Always try defaults first.** On CEH Practical, half of "login brute force" questions are solved by one guess from this table.

---

## 🎯 Top-30 that solve most exam questions

| Product / service | User | Password |
|---|---|---|
| Generic admin | `admin` | `admin` |
| Generic admin | `admin` | `password` |
| Generic admin | `administrator` | `administrator` |
| Generic root | `root` | `root` |
| Generic root | `root` | `toor` |
| Generic root | `root` | *(blank)* |
| Tomcat manager | `tomcat` | `tomcat` / `s3cret` |
| Tomcat manager | `admin` | `admin` |
| Apache Tomcat | `role1` | `role1` |
| Jenkins | `admin` | `admin` / `password` |
| phpMyAdmin | `root` | *(blank)* |
| MySQL | `root` | *(blank)* |
| PostgreSQL | `postgres` | `postgres` |
| MongoDB | *(no auth)* | — |
| Redis | *(no auth)* | — |
| Oracle | `scott` | `tiger` |
| Oracle | `system` | `manager` |
| Oracle | `sys` | `change_on_install` |
| MSSQL | `sa` | *(blank)* / `sa` |
| Cisco | `cisco` | `cisco` |
| Cisco | `admin` | `cisco` |
| HP iLO | `Administrator` | `(serial)` |
| Dell iDRAC | `root` | `calvin` |
| WebLogic | `weblogic` | `weblogic` / `welcome1` |
| JBoss | `admin` | `admin` |
| WordPress | `admin` | `admin` / `password` |
| Joomla | `admin` | `admin` |
| Drupal | `admin` | `admin` |
| Grafana | `admin` | `admin` |
| Kibana / Elastic | `elastic` | `changeme` |
| Rabbitmq | `guest` | `guest` |
| Jupyter | *(no auth / token)* | — |
| FTP anonymous | `anonymous` | `anonymous@` / blank |
| SNMP | community `public` / `private` | |
| VNC | *(no auth)* / `password` | |
| TeamViewer | — | — |
| Metasploitable | `msfadmin` | `msfadmin` |
| pfSense | `admin` | `pfsense` |
| Ubiquiti | `ubnt` | `ubnt` |
| Netgear | `admin` | `password` |
| Linksys | `admin` | `admin` |
| D-Link | `admin` | *(blank)* |
| TP-Link | `admin` | `admin` |
| HP printer | `admin` | *(blank)* |

---

## 📚 Authoritative lists

- **CIRT.net default passwords DB** — https://cirt.net/passwords — searchable per vendor.
- **SecLists** — https://github.com/danielmiessler/SecLists/tree/master/Passwords/Default-Credentials
- **Routerpasswords.com** — router/modem defaults.
- **datarecovery.com Default Passwords** — large vendor table.

---

## 📋 Recipe — quick spray

```bash
# Make a pairs file ("user:pass" per line)
cat > pairs.txt <<EOF
admin:admin
admin:password
root:root
root:toor
tomcat:tomcat
sa:
EOF

# Hydra against HTTP form login
hydra -C pairs.txt target http-post-form \
  "/login:user=^USER^&pass=^PASS^:F=Invalid"

# Or CrackMapExec against SMB
crackmapexec smb 10.10.10.0/24 -u users.txt -p passes.txt
```

---

## ⚠️ Gotchas

- Always try **blank password** and **username as password** — common for demo builds.
- Embedded devices often use the device **serial number** — hunt stickers in photos / firmware.
- Some products (HP iLO 5+, modern Cisco) randomise the factory password per device — check the physical label.
- Account lockout is real — use small, targeted lists before large sprays.

---

## 🔗 Related

- [hydra](hydra.md) · [crackmapexec](crackmapexec.md) · [medusa] · [nmap](nmap.md) (`*-brute` scripts)
