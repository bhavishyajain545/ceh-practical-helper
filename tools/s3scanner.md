# s3scanner — open S3 bucket finder

> **Tests S3 bucket names for existence + public read/write/ACL.** Works for AWS and S3-compatible clouds (DO Spaces, GCP, Linode).

**Install:** `pip install s3scanner` (or `go install github.com/sa7mon/s3scanner@latest`)

---

## 🎯 Cheat-flow

```bash
s3scanner scan --bucket acme-dev                      # single
s3scanner scan --buckets-file buckets.txt             # list
s3scanner scan --bucket acme --dump                   # scan + download contents
s3scanner scan --bucket acme --provider aws           # provider override
s3scanner scan --threads 20 -f buckets.txt            # threads
```

---

## 🔑 Flags

| Flag | Meaning |
|---|---|
| `--bucket <name>` | Single bucket |
| `--buckets-file <file>` | Newline list |
| `--provider <name>` | `aws` (default), `digitalocean`, `gcp`, `linode`, `custom` |
| `--dump` | Download all objects from public buckets |
| `--enumerate` | List objects |
| `--threads <N>` | Concurrency |
| `--json` | JSON output |

---

## 🟢 Result interpretation

| Result | Meaning |
|---|---|
| `not_exist` | No bucket |
| `exists` | Bucket exists but private |
| `AuthUsers [Read]` | Any AWS account can read |
| `AllUsers [Read]` | **Public — anonymous read** |
| `AllUsers [Read, Write]` | **Public write too — critical** |
| `AllUsers [ReadACP]` | Can read the ACL |

---

## 📋 Recipes

```bash
# 1. From cloud_enum output
cloud_enum -k acme | grep amazonaws | awk '{print $NF}' > buckets.txt
s3scanner scan --buckets-file buckets.txt

# 2. Dump contents of an open bucket
s3scanner scan --bucket acme-public --dump
# or
aws s3 ls --no-sign-request s3://acme-public
aws s3 sync --no-sign-request s3://acme-public ./loot/

# 3. Test common naming patterns
printf "%s\n" acme acme-backup acme-dev acme-prod acme-logs acme-uploads > b.txt
s3scanner scan --buckets-file b.txt
```

---

## ⚠️ Gotchas

- Bucket names are **global** — no region needed to test existence.
- AWS now blocks public buckets by default; older ones persist.
- Authenticated read (`AuthUsers`) requires any valid AWS account — `aws sts get-caller-identity` first, then retry.
- Some regions require `--region` explicitly with aws cli.

---

## 🔗 Related

- [aws-cli](aws-cli.md) · [cloud_enum](cloud_enum.md) · [gsutil](gsutil.md)
