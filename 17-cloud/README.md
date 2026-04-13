# 17 — Cloud

> Cloud questions in CEH Practical are almost always **S3 bucket enumeration**: find a publicly readable bucket, list it, download a flag/file. Occasionally Azure blobs or GCP buckets.

## 🧭 Decision tree — "I see a cloud question"

```
Bucket / storage target?
│
├── AWS S3
│   ├── Bucket name guessed / given
│   │   ├── aws s3 ls s3://<BUCKET> --no-sign-request
│   │   ├── aws s3 ls s3://<BUCKET> --recursive --no-sign-request
│   │   └── aws s3 cp s3://<BUCKET>/<FILE> . --no-sign-request
│   ├── Bucket name unknown
│   │   ├── cloud_enum -k <COMPANY>
│   │   ├── s3scanner scan --bucket <NAME>
│   │   └── Try common patterns: <company>-backup, <company>-dev, <company>-logs
│   └── HTTP listing (works in browser)
│       └── http://<BUCKET>.s3.amazonaws.com/
│
├── Azure Blob
│   ├── https://<ACCOUNT>.blob.core.windows.net/<CONTAINER>/?restype=container&comp=list
│   └── cloud_enum -k <COMPANY>
│
├── GCP Storage
│   ├── gsutil ls gs://<BUCKET>/
│   └── https://storage.googleapis.com/<BUCKET>/
│
└── "Creds leaked / found on host"
    ├── aws configure             → paste AK / SK
    ├── aws sts get-caller-identity
    └── aws s3 ls                 → list all buckets you can see
```

## 📄 Files in this folder

- **[commands.md](commands.md)** — aws cli / cloud_enum / s3 recipes
- **[walkthroughs.md](walkthroughs.md)** — full bucket enum examples
- Question bank: **[../questions/17-cloud.md](../questions/17-cloud.md)**

## 🛠 Tools used in this domain

- **[aws-cli](../tools/aws-cli.md)** — the workhorse
- **[cloud_enum](../tools/cloud_enum.md)** — multi-cloud bucket finder
- **[s3scanner](../tools/s3scanner.md)** — S3-only bucket scanner
- **[gsutil](../tools/gsutil.md)** — GCP CLI

## ✅ Domain checklist

- [ ] `aws s3 ls s3://<BUCKET> --no-sign-request` from memory
- [ ] Know the `--recursive` flag for full listings
- [ ] Download a file with `aws s3 cp s3://.../file .`
- [ ] Know the S3 HTTP URL pattern: `http://<BUCKET>.s3.amazonaws.com/`
- [ ] Run cloud_enum against a keyword
- [ ] Know where common creds hide (`~/.aws/credentials`, env vars, `.bash_history`)
- [ ] Done all questions in [the question bank](../questions/17-cloud.md)
