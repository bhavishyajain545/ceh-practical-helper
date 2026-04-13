# 15 Mobile — copy-paste commands

> Replace `<APK>` with the APK file name (e.g. `app.apk`).

## apktool — decode

```bash
# Decode (resources + smali)
apktool d <APK> -o app_out

# Skip resources (faster, smali only)
apktool d -r <APK> -o app_out

# Rebuild after edits
apktool b app_out -o patched.apk
```

→ See [apktool.md](../tools/apktool.md)

## jadx — decompile to Java

```bash
# GUI — easiest for exam
jadx-gui <APK>

# CLI dump all Java sources
jadx -d out <APK>

# Dump including resources
jadx -d out --show-bad-code <APK>
```

→ See [jadx.md](../tools/jadx.md)

## dex2jar + JD-GUI fallback

```bash
d2j-dex2jar <APK> -o app.jar
jd-gui app.jar
```

## Grep for secrets in decoded output

```bash
# URLs
grep -rE 'https?://' app_out/ | grep -v schemas.android.com

# API keys / tokens / passwords
grep -rEi 'api[_-]?key|token|secret|passw|bearer' app_out/

# AWS keys
grep -rE 'AKIA[0-9A-Z]{16}' app_out/

# Everything in strings.xml
cat app_out/res/values/strings.xml
```

## AndroidManifest.xml — what to read

```bash
# Permissions
grep 'uses-permission' app_out/AndroidManifest.xml

# Exported components (attack surface)
grep -E 'exported="true"' app_out/AndroidManifest.xml

# Main activity / package name
grep -E 'package=|MAIN' app_out/AndroidManifest.xml
```

## adb — talk to a device / emulator

```bash
# List connected devices
adb devices

# Install
adb install <APK>
adb uninstall com.example.app

# Shell
adb shell

# Pull an APK off a device
adb shell pm path com.example.app
adb pull /data/app/com.example.app-1/base.apk

# Logcat for a package
adb logcat | grep com.example.app
```

→ See [adb.md](../tools/adb.md)

## MobSF — automated analysis

```bash
# Docker one-liner
docker run -it --rm -p 8000:8000 opensecurity/mobile-security-framework-mobsf
# Then browse http://127.0.0.1:8000 and drag the APK
```

## Binary-level grep (skip decode)

```bash
strings <APK> | grep -Ei 'http|api|key|token'
unzip -p <APK> classes.dex | strings | grep -i key
```
