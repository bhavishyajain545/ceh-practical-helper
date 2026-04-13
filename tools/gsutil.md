# gsutil — Google Cloud Storage CLI

> **GCP's S3-equivalent tool.** List, copy, sync GCS buckets.

**Install check:** `gsutil version` (part of the google-cloud-sdk)

---

## 🎯 Cheat-flow

```bash
gsutil ls                              # all projects' buckets you can see
gsutil ls gs://bucket                  # contents
gsutil ls -r gs://bucket               # recursive
gsutil cat gs://bucket/file.txt        # print object
gsutil cp gs://bucket/file.txt .       # download
gsutil cp -r gs://bucket ./local       # download recursive
gsutil -m cp -r gs://bucket ./local    # parallel multi-thread
gsutil iam get gs://bucket             # IAM bindings
gsutil acl get gs://bucket             # legacy ACL
gsutil du -sh gs://bucket              # size
```

Anonymous access — no login needed:
```bash
gsutil -o "Credentials:gs_service_key_file=" ls gs://bucket
# or just use curl
curl https://storage.googleapis.com/bucket/
```

---

## 🔑 Common commands

| Command | Purpose |
|---|---|
| `gsutil ls [-L] gs://bucket` | List (verbose with `-L`) |
| `gsutil cp <src> <dst>` | Copy |
| `gsutil rsync -r <src> <dst>` | Rsync |
| `gsutil rm gs://bucket/obj` | Delete |
| `gsutil iam get/set` | IAM |
| `gsutil acl get/set` | ACL (legacy) |
| `gsutil signurl` | Pre-signed URL |
| `gsutil web set` | Static site config |
| `gsutil -m` | Parallel (fast for many files) |

---

## 📋 Recipes

```bash
# 1. Test a guessed bucket anonymously
curl -s -o /dev/null -w "%{http_code}\n" https://storage.googleapis.com/acme-dev/
# 200 = public list; 403 = exists but private; 404 = not found

# 2. Dump a whole open bucket
gsutil -m cp -r gs://acme-public ./loot/

# 3. From found SA key (.json)
gcloud auth activate-service-account --key-file=key.json
gsutil ls
```

---

## ⚠️ Gotchas

- GCS bucket names are also global like S3.
- `gcloud` (sibling tool) handles IAM, compute, and most other GCP services; gsutil is storage-only.
- For unauthenticated probing, HTTPS GET to `storage.googleapis.com/<bucket>/` works fine without the SDK.
- Use `gsutil -m` for speed but be aware of bandwidth.

---

## 🔗 Related

- [aws-cli](aws-cli.md) · [s3scanner](s3scanner.md) · [cloud_enum](cloud_enum.md)
