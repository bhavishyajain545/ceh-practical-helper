# Q131 — DNS Spoofing Ettercap

| Field | Value |
|---|---|
| **Target** | `192.168.52.129` / `192.168.52.130` / pcap |
| **Domain** | 07 — Sniffing / 14 — Wireless |
| **Difficulty** | 🟡 Medium |
| **Tools** | `ettercap` |
| **Time budget** | 10–20 min |

---

## 📝 Question

Configure ettercap with `etter.dns` to spoof `google.com → 192.168.52.128`. Provide entry format.

---

## 🎯 Flag Format

```
entry=<line>
```

Example: `entry=google.com A 192.168.52.128`

**⚠️ Format strict hai** — lowercase, no spaces around `=`, semicolon separator where shown.

---

## 💡 Hints

<details>
<summary>Hint 1 (click)</summary>

Edit `/etc/ettercap/etter.dns`.
</details>

<details>
<summary>Hint 2</summary>

Then load plugin `dns_spoof`.
</details>

---

## ✅ Solution

<details>
<summary>Show solution</summary>

```bash
echo 'google.com A 192.168.52.128' | sudo tee -a /etc/ettercap/etter.dns
```

Victims resolving google.com hit Parrot.

**Answer:** `entry=google.com A 192.168.52.128`

📖 Ref: [tools/ettercap.md](../../tools/ettercap.md)
</details>

---

## 🤖 Claude Setup Prompt (for Claude-on-your-PC)

```
Pre-requisites for Q131:
1. Verify Metasploitable2 up.
2. On Parrot edit /etc/ettercap/etter.dns (sudo), append 'google.com A 192.168.52.128'.
3. Ensure dns_spoof plugin available (part of ettercap install).

Report back: "Lab ready for Q131 — ettercap installed, etter.dns editable".
```
