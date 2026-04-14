# Q010 — Scan Windows 7 Target

| Field | Value |
|---|---|
| **Target** | `192.168.52.130` (Windows 7) |
| **Domain** | 02 — Scanning |
| **Difficulty** | 🟢 Easy |
| **Tools** | `nmap`, `hping3`, `masscan`, `nc` |
| **Time budget** | 5–10 min |

---

## 📝 Question

Run a SYN+version scan on Windows 7 (192.168.52.130) and identify whether **port 445 (SMB)** is open. Also report the OS detected.

---

## 🎯 Flag Format

```
smb445=<open|closed>; os=<windows7|windows10>
```

Example: `smb445=open; os=windows7`

**⚠️ Format strict hai** — lowercase, no spaces around `=`, semicolon separator where shown.

---

## 💡 Hints

<details>
<summary>Hint 1 (click)</summary>

`sudo nmap -sS -sV -O <ip>`
</details>

<details>
<summary>Hint 2</summary>

445/tcp microsoft-ds typically open by default on Win7.
</details>

---

## ✅ Solution

<details>
<summary>Show solution</summary>

```bash
sudo nmap -sS -sV -O 192.168.52.130
```

Port 445 open; OS = Windows 7.

**Answer:** `smb445=open; os=windows7`

📖 Ref: [tools/nmap.md](../../tools/nmap.md)
</details>

---

## 🤖 Claude Setup Prompt (for Claude-on-your-PC)

```
1. Win7 VM running; Windows Firewall configured to allow file sharing on internal NIC.
2. Verify ping from Parrot.

Report back: "Lab ready for Q010".
```
