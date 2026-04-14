# Q002 — TCP Connect Scan vs SYN Scan

| Field | Value |
|---|---|
| **Target** | `192.168.52.129` (Metasploitable 2) |
| **Domain** | 02 — Scanning |
| **Difficulty** | 🟢 Easy |
| **Tools** | `nmap`, `hping3`, `masscan`, `nc` |
| **Time budget** | 5–10 min |

---

## 📝 Question

Run a full TCP connect scan (`-sT`) against Metasploitable and report the **number of open ports** plus the **highest port number** found open.

---

## 🎯 Flag Format

```
open=<n>; highest=<port>
```

Example: `open=23; highest=8180`

**⚠️ Format strict hai** — lowercase, no spaces around `=`, semicolon separator where shown.

---

## 💡 Hints

<details>
<summary>Hint 1 (click)</summary>

`nmap -sT` does a full 3-way handshake — slower but no root needed.
</details>

<details>
<summary>Hint 2</summary>

Sort the output or look at the last line of the open-ports list to find the highest port.
</details>

---

## ✅ Solution

<details>
<summary>Show solution</summary>

```bash
nmap -sT 192.168.52.129
```

Default top-1000 scan reveals 23 open ports; highest commonly **8180** (Tomcat).

**Answer:** `open=23; highest=8180`

📖 Ref: [tools/nmap.md](../../tools/nmap.md)
</details>

---

## 🤖 Claude Setup Prompt (for Claude-on-your-PC)

```
1. Verify 192.168.52.129 is reachable from 192.168.52.128 (ping).
2. Ensure all default Metasploitable services up.
3. No extra setup needed.

Report back: "Lab ready for Q002".
```
