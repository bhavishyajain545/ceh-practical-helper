# Q006 — hping3 SYN Probe

| Field | Value |
|---|---|
| **Target** | `192.168.52.129` (Metasploitable 2) |
| **Domain** | 02 — Scanning |
| **Difficulty** | 🟢 Easy |
| **Tools** | `hping3` |
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
Pre-requisites for Q006:
1. Verify Metasploitable2 (192.168.52.129) is reachable: ping -c 2 192.168.52.129.
2. Confirm hping3 installed on Parrot: which hping3 → /usr/sbin/hping3. If missing: sudo apt install hping3.
3. hping3 crafts raw packets → needs root. Confirm: sudo -n true || sudo -v.
4. Apache on Metasploitable port 80 must be listening (that's what sends back SYN/ACK):
   - Quick test: curl -sI http://192.168.52.129/ | head -1 should return HTTP/1.1 200 OK.
   - If down: ssh msfadmin@192.168.52.129 → sudo service apache2 start.

Report back: "Lab ready for Q006 — hping3 present, apache on 80 responsive".
```
