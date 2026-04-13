# trufflehog — secrets scanner

> **Finds committed secrets in git history, filesystems, S3, Docker images, etc.** Verifies live ones via API.

**Install check:** `trufflehog --version`

---

## 🎯 Cheat-flow

```bash
trufflehog git https://github.com/org/repo              # public git repo
trufflehog git file:///path/to/local/repo               # local
trufflehog filesystem /path/to/dir                      # disk scan
trufflehog github --org=acme                            # all repos in GH org
trufflehog gitlab --token=<pat>                         # GitLab
trufflehog s3 --bucket=target --no-verification         # S3
trufflehog docker --image=nginx:latest                  # image layers
trufflehog --only-verified git https://github.com/org/repo   # live keys only
trufflehog --json git ...  | jq .                       # JSON
```

---

## 🔑 Global flags

| Flag | Meaning |
|---|---|
| `--only-verified` | **Show only secrets that pass live verification** |
| `--json` | JSON output |
| `--no-verification` | Skip live verification |
| `--include-paths <file>` | Path regex allow-list |
| `--exclude-paths <file>` | Path regex deny-list |
| `--since-commit <sha>` | Git: start from commit |
| `--branch <name>` | Git: branch |
| `--max-depth <N>` | Git: commit depth |
| `--concurrency <N>` | Workers |
| `--fail` | Exit non-zero on findings (CI) |

---

## 📋 Recipes

```bash
# 1. Fast scan a checked-out repo for verified secrets
trufflehog --only-verified git file://$PWD

# 2. Org-wide scan
trufflehog github --org=acme --token=<GH_PAT> --only-verified --json > secrets.json

# 3. Scan an S3 bucket for creds in files
trufflehog s3 --bucket=backups --only-verified

# 4. CI gate
trufflehog --fail --only-verified git file://$PWD
```

---

## 🔑 What it detects (sample)

AWS keys, GCP service accounts, Azure keys, GitHub tokens, Slack tokens, Stripe, Twilio, SendGrid, Mailgun, Heroku, private RSA/EC keys, JWT secrets, DB URIs, Docker Hub, npm tokens, PyPI tokens, ... (800+ detectors).

---

## ⚠️ Gotchas

- **v3 rewrote the tool in Go** — v2 docs on the internet are out of date.
- Default shows *unverified* findings too (high noise) → use `--only-verified` for actionable only.
- Verification makes live API calls → may tip off defenders on blue-team engagements.
- Git scans look at **entire history** — large repos take minutes.

---

## 🔗 Related

- [aws-cli](aws-cli.md) · [virustotal](virustotal.md)
