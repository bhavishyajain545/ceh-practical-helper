# Q006 — hping3 SYN Probe

| Field | Value |
|---|---|
| **Target** | `192.168.52.129` (Metasploitable 2) |
| **Domain** | 02 — Scanning |
| **Difficulty** | 🟢 Easy |
| **Tools** | `nmap`, `hping3`, `masscan`, `nc` |
| **Time budget** | 5–10 min |

---

## 📝 Question

Use `hping3` to send 5 SYN packets to port 80 of Metasploitable and confirm the port is open. Report the **TCP flag** seen in the reply.

---

## 🎯 Flag Format

```
flags=<flagstring>
```

Example: `flags=SA`

**⚠️ Format strict hai** — lowercase, no spaces around `=`, semicolon separator where shown.

---

## 💡 Hints

<details>
<summary>Hint 1 (click)</summary>

`hping3 -S -p 80 -c 5 <ip>` sends SYN packets.
</details>

<details>
<summary>Hint 2</summary>

Open port replies with SYN+ACK — `flags=SA` in hping3 output.
</details>

---

## ✅ Solution

<details>
<summary>Show solution</summary>

```bash
sudo hping3 -S -p 80 -c 5 192.168.52.129
```

Replies show `flags=SA` (SYN/ACK) confirming the port is open.

**Answer:** `flags=SA`

📖 Ref: [tools/hping3.md](../../tools/hping3.md)
</details>

---

## 🤖 Claude Setup Prompt (for Claude-on-your-PC)

```
1. Metasploitable apache running on 80.

Report back: "Lab ready for Q006".
```
