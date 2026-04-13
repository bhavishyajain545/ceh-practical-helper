# mobsf — Mobile Security Framework

> **All-in-one static + dynamic mobile app scanner.** Accepts APK, IPA, APPX, ZIP of source.

**Launch:** `docker run -it --rm -p 8000:8000 opensecurity/mobile-security-framework-mobsf:latest` → open http://127.0.0.1:8000

---

## 🎯 Cheat-flow

1. Open web UI at http://127.0.0.1:8000.
2. Drag-drop `app.apk` (or IPA, APPX, ZIP source).
3. Wait for the static analysis to finish (1–3 min).
4. Read the report tabs in order:
   - **Information** — package, version, main activity
   - **Scan options** / **Certificate**
   - **Permissions** — dangerous perms highlighted
   - **Android API** — crypto, reflection, WebView misuse
   - **Browsable activities** — deep links (intent attack surface)
   - **Security Analysis** — MASVS-aligned findings
   - **Manifest Analysis** — exported components, backup flag, debuggable flag
   - **Code Analysis** — regex-based secret + API misuse
   - **URLs / Emails / Strings / Firebase DB** — leaks
   - **Malware checks / Domain Malware / Abused permissions**

---

## 🔑 Static vs Dynamic

- **Static** — just upload; works offline.
- **Dynamic** — needs a real Android VM or connected device running MobSFy'd image; MobSF drives the app, watches traffic, dumps data.

---

## 📋 REST API (scriptable)

```bash
# Upload
curl -F 'file=@app.apk' -H "Authorization: $TOKEN" http://127.0.0.1:8000/api/v1/upload

# Scan
curl -X POST -H "Authorization: $TOKEN" \
     -d "scan_type=apk&file_name=app.apk&hash=<md5>" \
     http://127.0.0.1:8000/api/v1/scan

# JSON report
curl -X POST -H "Authorization: $TOKEN" -d "hash=<md5>" \
     http://127.0.0.1:8000/api/v1/report_json
```

Token is in `~/.MobSF/config.py` or printed at startup.

---

## ⚠️ Gotchas

- Dynamic analyzer needs MobSFy'd Genymotion/AVD — not trivial.
- Docker image is ~2 GB; first run downloads rules.
- Secrets finder uses regex → false positives; verify manually.
- Reports export as PDF from the UI; great for CEH write-ups.

---

## 🔗 Related

- [apktool](apktool.md) · [jadx](jadx.md) · [dex2jar](dex2jar.md) · [adb](adb.md)
