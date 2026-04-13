# cloud_enum — multi-cloud asset enumeration

> **Find public AWS/Azure/GCP resources tied to a keyword.** Perfect for cloud footprinting questions.

**Install:** `git clone https://github.com/initstring/cloud_enum && pip install -r requirements.txt`

---

## 🎯 Cheat-flow

```bash
python3 cloud_enum.py -k target                    # single keyword
python3 cloud_enum.py -k target -k target-dev      # multiple
python3 cloud_enum.py -kf keywords.txt             # keyword file
python3 cloud_enum.py -k target --disable-aws      # skip AWS
python3 cloud_enum.py -k target --disable-azure --disable-gcp
python3 cloud_enum.py -k target -l out.txt         # log file
python3 cloud_enum.py -k target -t 10              # threads
```

---

## 🔑 Flags

| Flag | Meaning |
|---|---|
| `-k <word>` | Keyword (repeatable) |
| `-kf <file>` | Keyword file |
| `-m <file>` | Mutations wordlist |
| `-b <file>` | Brute list (additional suffixes) |
| `-t <N>` | Threads |
| `-l <file>` | Output log |
| `-f <fmt>` | Output format |
| `--disable-aws` / `--disable-azure` / `--disable-gcp` | Skip a cloud |
| `-qs` | Quick scan (fewer permutations) |

---

## 🧪 What it checks

| Cloud | Resources |
|---|---|
| **AWS** | S3 buckets (public + private existence), `awsapps.com` (WorkDocs/WorkMail) |
| **Azure** | `blob.core.windows.net`, `file.core.windows.net`, `queue.core.windows.net`, `table.core.windows.net`, `database.windows.net`, `vault.azure.net`, Azure Websites (`azurewebsites.net`) |
| **GCP** | `storage.googleapis.com` buckets, `appspot.com`, GCP Firebase |

Output uses OPEN / AUTH / UNKNOWN to indicate public vs exists-but-private.

---

## 📋 Recipes

```bash
# 1. Keyword + common mutations
python3 cloud_enum.py -k acme -k acme-corp -k acmecorp -l acme.txt

# 2. Exclude Azure (long scan times)
python3 cloud_enum.py -k acme --disable-azure

# 3. Feed results to s3scanner for deeper AWS enum
grep 's3\.amazonaws' acme.txt | cut -d' ' -f2 > buckets.txt
s3scanner scan --buckets-file buckets.txt
```

---

## ⚠️ Gotchas

- Azure enumeration is **slow** — it brute-forces DNS for every sub-service.
- Rate-limiting: GCP returns 429 — lower threads with `-t`.
- Tool only shows *existence*; test access separately (aws cli, s3scanner, gsutil).
- Wordlist mutation can explode — prefer a short `-kf` list.

---

## 🔗 Related

- [s3scanner](s3scanner.md) · [aws-cli](aws-cli.md) · [gsutil](gsutil.md) · [shodan](shodan.md)
