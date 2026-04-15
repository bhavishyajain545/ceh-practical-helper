# veracrypt — encrypted volumes / containers

> **The successor to TrueCrypt.** Mounts `.vc` / `.hc` container files, full-disk encryption, and hidden volumes. CEH Practical scenario-09 gives you a container + password and expects you to mount it, list contents, and pull out a flag — all from the CLI (`--text`).

**Install check (Parrot):** `veracrypt --version`
**Install if missing:** `sudo apt install veracrypt` *(or download AppImage from veracrypt.fr)*

---

## 🎯 Cheat-flow: "Mount a .vc file from CLI"

| Step | Command |
|---|---|
| 1. Create mount dir | `sudo mkdir -p /mnt/vc` |
| 2. **Mount container** | `sudo veracrypt --text --mount volume.vc /mnt/vc --password='MyPass' --pim=0 --keyfiles= --protect-hidden=no` |
| 3. List contents | `ls -la /mnt/vc` |
| 4. Unmount when done | `sudo veracrypt --text --dismount /mnt/vc` |
| List all mounted | `veracrypt --text --list` |

---

## 🔑 CLI flags (`--text` mode = non-interactive)

| Flag | Meaning |
|---|---|
| `--text` / `-t` | **Text/CLI mode** (no GUI prompt) — required for scripting |
| `--mount <file> <mountpoint>` | Mount this container at this path |
| `--dismount [<mountpoint>]` | Unmount; no arg = unmount all |
| `--list` | Show currently mounted VeraCrypt volumes |
| `--password=<pass>` | Password for the volume |
| `--pim=<n>` | **Personal Iterations Multiplier** (use `0` for default) |
| `--keyfiles=<path>` | Keyfiles (empty after `=` means none) |
| `--protect-hidden=yes/no` | Protect hidden volume when mounting outer |
| `--hash=<algo>` | Force hash (`sha-512`, `sha-256`, `whirlpool`, `streebog`) — speeds up mount if you know it |
| `--encryption=<algo>` | Force cipher (rare) |
| `--slot=<n>` | Mount at drive slot N |
| `--read-only` | Mount read-only |
| `--filesystem=<fs>` | Force filesystem (usually auto) |
| `--non-interactive` | Fail instead of prompting if anything is missing |
| `--force` | Skip some safety checks |
| `--mount-options=<opts>` | Pass options to mount (e.g. `ro,noatime`) |
| `--volume-type=normal\|hidden` | Choose outer vs hidden |
| `--stdin` | Read password from stdin |
| `--create <file>` | Create a new container (interactive) |
| `--create-keyfile <file>` | Generate a random keyfile |
| `--backup-headers <file>` | Backup volume headers |
| `--restore-headers <file>` | Restore headers (use if volume is corrupted) |
| `--change <file>` | Change password/keyfiles |

---

## 📋 Command recipes

```bash
# 1. Standard mount (scenario-09 style)
sudo mkdir -p /mnt/vc
sudo veracrypt --text --mount secret.vc /mnt/vc \
  --password='Welcome123!' --pim=0 --keyfiles= --protect-hidden=no
ls -la /mnt/vc
sudo veracrypt --text --dismount /mnt/vc

# 2. Read-only mount (forensics — don't alter evidence)
sudo veracrypt --text --mount -o ro secret.vc /mnt/vc \
  --password='Pass' --pim=0 --keyfiles= --protect-hidden=no --read-only

# 3. Mount a hidden volume
sudo veracrypt --text --mount hidden.vc /mnt/hidden \
  --password='HiddenPass' --pim=0 --keyfiles= \
  --protect-hidden=no --volume-type=hidden

# 4. Specify hash to skip auto-detect (faster mount)
sudo veracrypt --text --mount vol.vc /mnt/vc \
  --password='p' --pim=0 --keyfiles= --protect-hidden=no \
  --hash=sha-512

# 5. List all mounts
veracrypt --text --list

# 6. Unmount everything
sudo veracrypt --text --dismount

# 7. Backup volume headers (save before experimenting)
sudo veracrypt --text --backup-headers secret.vc
# saves to secret.vc.header.bak or prompts

# 8. Create a new container (prompts, even in --text)
veracrypt --text --create container.vc
```

---

## 🧪 Hidden volumes (plausible deniability)

A VeraCrypt container can hide a **second** volume inside free space of the outer one. You reveal one password under duress, the real secrets stay invisible.

- Mount outer: `--volume-type=normal`
- Mount hidden: `--volume-type=hidden` (different password)
- **Protect hidden when writing to outer:** `--protect-hidden=yes` + pass the hidden password too (else writes to outer can overwrite the hidden volume).

CEH may ask: "The volume has two passwords — what's the concept?" → **hidden volume / plausible deniability**.

---

## 🔐 Cracking VeraCrypt containers with hashcat

When the password is unknown, hashcat modes (offline, very slow — PBKDF2 with ~500k iterations):

| `-m` | Cipher / hash | Use |
|---|---|---|
| `13711` | **VeraCrypt SHA-512 + AES** | Default modern container |
| `13712` | VeraCrypt SHA-512 + Serpent | |
| `13713` | VeraCrypt SHA-512 + Twofish | |
| `13721` | VeraCrypt SHA-256 + AES | |
| `13722` | VeraCrypt SHA-256 + Serpent | |
| `13723` | VeraCrypt SHA-256 + Twofish | |
| `13731` | VeraCrypt Whirlpool + AES | |
| `13732` | Whirlpool + Serpent | |
| `13733` | Whirlpool + Twofish | |
| `13741` | **VeraCrypt SHA-512 + AES (boot)** | System drive mode |
| `13751` | RIPEMD160 + AES | TrueCrypt-compat mode |
| `13771` | Streebog-512 + AES | Russian GOST |

```bash
# Extract first 512 bytes of container for hashcat, then crack
hashcat -m 13711 container.vc /usr/share/wordlists/rockyou.txt
```

Use `hashid` / the file's creation tool to narrow down which mode.

---

## 🖥 GUI quick tour (for reference)

1. Launch `veracrypt` (no args) → GUI opens
2. Click a drive slot → **Select File...** → browse to `.vc`
3. **Mount** → enter password (and PIM/keyfiles if used)
4. Container appears as a mounted drive — open in file manager
5. **Dismount** when done (data persists in file only when unmounted)

---

## ⚠️ Gotchas

- **Always need `sudo`** for mount/dismount — it talks to FUSE/device layer.
- **`--pim=0` is the default** value. Leave it unless the creator set a custom PIM — then mount fails until you pass the correct number.
- **`--keyfiles=` with nothing after** means "no keyfiles". Omitting the flag entirely prompts interactively in `--text` mode.
- **Hidden volume protection:** when you mount the outer to write data, always pass `--protect-hidden=yes` + the hidden password or you can corrupt it.
- **Wrong hash** → mount just says "Incorrect password" after a long wait. Try specifying `--hash=sha-512` to speed up diagnosis.
- **Headers get corrupted?** Use `--restore-headers` from a backup or the built-in embedded backup at the end of the volume.
- **File extension doesn't matter** (`.vc` / `.hc` / no ext). VeraCrypt identifies by header, not name.
- **`veracrypt --text --list`** returns exit 1 if nothing mounted — scripts need `|| true`.
- **Cracking is slow** — 500,000 PBKDF2 iterations means a CPU will do <10 H/s. GPU + small wordlist only.
- **AppImage path** can cause "sudo: veracrypt: command not found" — use full path or `alias sudo='sudo '`.

---

## 🔗 Related

- [hashcat](hashcat.md) — `-m 13711` and friends for cracking
- [steghide](steghide.md) — often chained with VeraCrypt in exam scenarios (stego image → password → `.vc` → flag)
- [openssl](openssl.md) — general symmetric crypto alternative
- [file](file.md) — identify file type before assuming it's VeraCrypt
- [binwalk](binwalk.md) — check for hidden embedded data
- [18-cryptography domain README](../18-cryptography/README.md)
