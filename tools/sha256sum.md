# sha256sum — see md5sum

> **SHA-256 variant of the coreutils hash family.** Identical flags to [md5sum](md5sum.md).

```bash
sha256sum file.bin
sha256sum -c hashes.txt
find . -type f -exec sha256sum {} \; > sums.txt
```

See [md5sum](md5sum.md) for full flag reference and recipes. Same tool, different algorithm:

| Tool | Digest | Hex chars |
|---|---|---|
| `md5sum` | MD5 | 32 |
| `sha1sum` | SHA-1 | 40 |
| `sha256sum` | SHA-256 | 64 |
| `sha512sum` | SHA-512 | 128 |

**Use sha256sum when** the exam question says "sha256", or you're verifying a modern distro ISO, or submitting to VirusTotal (VT indexes by sha256).

---

## 🔗 Related

- [md5sum](md5sum.md) · [virustotal](virustotal.md) · [hashid](hashid.md)
