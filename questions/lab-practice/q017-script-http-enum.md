# Q017 — NSE http-enum on Apache

| Field | Value |
|---|---|
| **Target** | `192.168.52.129` (Metasploitable 2) |
| **Domain** | 02 — Scanning |
| **Difficulty** | 🟡 Medium |
| **Tools** | `nmap` |
| **Time budget** | 5–10 min |

---

## 📝 Question

Use NSE script `http-enum` on Metasploitable port 80 and report **one discovered web app path** (any).

---

## 🎯 Flag Format

```
path=</path>
```

Example: `path=/phpMyAdmin/`

**⚠️ Format strict hai** — lowercase, no spaces around `=`, semicolon separator where shown.

---

## 💡 Hints

<details>
<summary>Hint 1 (click)</summary>

`nmap --script http-enum -p 80 <ip>`
</details>

<details>
<summary>Hint 2</summary>

Common discoveries: /phpMyAdmin/, /tikiwiki/, /dvwa/, /mutillidae/.
</details>

---

## ✅ Solution

<details>
<summary>Show solution</summary>

```bash
nmap --script http-enum -p 80 192.168.52.129
```

Lists multiple paths; pick any (e.g. /phpMyAdmin/).

**Answer:** `path=/phpMyAdmin/`

📖 Ref: [tools/nmap.md](../../tools/nmap.md)
</details>

---

## 🤖 Claude Setup Prompt (for Claude-on-your-PC)

```
Pre-requisites for Q017:
1. Verify Metasploitable2 (192.168.52.129) is reachable: ping -c 2 192.168.52.129.
2. Apache on port 80 must be up and serving the default webapps (DVWA, phpMyAdmin, tikiwiki, mutillidae, twiki) — those are the paths http-enum fingerprints:
   - Test: curl -sI http://192.168.52.129/ | head -1 → HTTP/1.1 200 OK.
   - Test: curl -s -o /dev/null -w "%{http_code}\n" http://192.168.52.129/phpMyAdmin/ → 200 or 301.
   - If apache down: ssh msfadmin@192.168.52.129 → sudo service apache2 start.
3. http-enum NSE script must exist: ls /usr/share/nmap/scripts/http-enum.nse. Shipped by default.
4. http-enum uses fingerprints db (http-fingerprints.lua). Ships with nmap, no internet required.
5. No root required (TCP connect to :80 only).

Report back: "Lab ready for Q017 — apache on 192.168.52.129:80 returning 200, http-enum.nse present".
```
