# openvas / Greenbone — full vulnerability scanner

> **Big enterprise-style vuln scanner.** 100k+ NVTs (Network Vulnerability Tests). CEH loves asking about it.

**Launch:** `sudo gvm-start` → browse to https://127.0.0.1:9392

---

## 🎯 Cheat-flow (web UI — GSA)

1. **Configuration → Targets** → New target with host(s) + port list.
2. **Scans → Tasks** → New task → pick target + scan config (Full and fast / Full and very deep).
3. Start task → wait (can be hours).
4. **Scans → Reports** → open report → filter by severity.
5. Export PDF / XML / CSV.

---

## 🔑 Setup / maintenance

```bash
sudo gvm-setup              # first-time setup (downloads NVTs, creates admin)
sudo gvm-start              # start services (gvmd, ospd-openvas, gsad)
sudo gvm-stop
sudo gvm-check-setup        # diagnose
sudo gvm-feed-update        # refresh NVT + CERT + SCAP feeds
sudo runuser -u _gvm -- greenbone-nvt-sync
```

Default admin created by `gvm-setup` — password printed once; reset with `sudo runuser -u _gvm -- gvmd --user=admin --new-password=NEW`.

---

## 🔑 Scan configs (built-in)

| Config | Behavior |
|---|---|
| **Discovery** | Host + port + service, no vulns |
| **Host Discovery** | Ping only |
| **System Discovery** | Host + OS + running services |
| **Full and fast** | Default — safe NVTs, optimised |
| **Full and fast ultimate** | Includes potentially harmful NVTs |
| **Full and very deep** | All NVTs, no optimisation |
| **Full and very deep ultimate** | All NVTs + harmful |

---

## 🔑 CLI (gvm-tools)

```bash
# Connect via socket
gvm-cli socket --xml "<get_version/>"

# Create target + task
gvm-cli --gmp-username admin --gmp-password PW socket \
  --xml '<create_target><name>t1</name><hosts>10.10.10.5</hosts></create_target>'

# List tasks
gvm-cli --gmp-username admin --gmp-password PW socket --xml "<get_tasks/>"
```

---

## 📋 Typical exam questions

- **"What severity does openvas rate CVE-X?"** → find the NVT in the report, read CVSS.
- **"How many high findings on host Y?"** → Reports → filter severity ≥ 7.
- **"Where is the scanner running?"** → port 9392 (GSA), 22/9390 (gvmd).

---

## ⚠️ Gotchas

- First feed sync is **slow** (30+ min).
- Services can conflict on ports — check `gvm-check-setup`.
- Authenticated scans need SMB/SSH credentials configured under **Configuration → Credentials**.
- Memory-hungry — give the VM ≥ 4 GB.
- Newer branding is **Greenbone Vulnerability Manager (GVM)** but CEH still calls it OpenVAS.

---

## 🔗 Related

- [nmap](nmap.md) · [nuclei](nuclei.md) · [nikto](nikto.md)
