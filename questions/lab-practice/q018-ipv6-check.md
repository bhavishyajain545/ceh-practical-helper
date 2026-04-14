# Q018 — IPv6 Reachability Check

| Field | Value |
|---|---|
| **Target** | `192.168.52.129` (Metasploitable 2) |
| **Domain** | 02 — Scanning |
| **Difficulty** | 🟢 Easy |
| **Tools** | `nmap`, `hping3`, `masscan`, `nc` |
| **Time budget** | 5–10 min |

---

## 📝 Question

Run `nmap -6` on the link-local IPv6 address of Metasploitable (auto-discovered via `ip -6 neigh`). What flag enables IPv6 in nmap?

---

## 🎯 Flag Format

```
flag=<nmap-flag>
```

Example: `flag=-6`

**⚠️ Format strict hai** — lowercase, no spaces around `=`, semicolon separator where shown.

---

## 💡 Hints

<details>
<summary>Hint 1 (click)</summary>

`-6` enables IPv6 mode.
</details>

<details>
<summary>Hint 2</summary>

Find Metasploitable v6 addr via `ping6 -I eth0 ff02::1`.
</details>

---

## ✅ Solution

<details>
<summary>Show solution</summary>

```bash
ping6 -c 2 -I eth0 ff02::1
sudo nmap -6 -sS fe80::XXXX%eth0
```

Scan via IPv6 link-local works.

**Answer:** `flag=-6`

📖 Ref: [tools/nmap.md](../../tools/nmap.md)
</details>

---

## 🤖 Claude Setup Prompt (for Claude-on-your-PC)

```
1. IPv6 enabled on both VMs (default).

Report back: "Lab ready for Q018".
```
