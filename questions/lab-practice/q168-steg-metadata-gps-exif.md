# Q168 — Extract GPS Coordinates from Image EXIF Data

| Field | Value |
|-------|-------|
| **Target** | local |
| **Domain** | Steganography |
| **Difficulty** | 🟢 Easy |
| **Tools** | `exiftool` |
| **Time budget** | 3–5 min |

---

## 📝 Question

"A photo `geotagged.jpg` was taken at an unknown location. Extract the GPS coordinates from EXIF data. Report the latitude and longitude."

---

## 🎯 Flag Format

```
lat=<degrees>; lon=<degrees>
```

Example: `lat=40.7128; lon=-74.0060`

**⚠️ Format strict hai** — lowercase, no spaces around `=`, semicolon separator where shown.

---

## 💡 Hints

**Hint 1**

`exiftool -gps* geotagged.jpg` se GPS related fields dikhenge.

**Hint 2**

`exiftool -GPSLatitude -GPSLongitude -n geotagged.jpg` — `-n` flag decimal format dega.

---

## ✅ Solution

```bash
exiftool -GPSLatitude -GPSLongitude -n geotagged.jpg
```

**Answer:** `lat=28.6139; lon=77.2090`

---

## 🤖 Claude Setup Prompt

1. Create geotagged JPEG:
```bash
convert -size 200x200 xc:green /tmp/geotagged.jpg
exiftool -GPSLatitude=28.6139 -GPSLongitude=77.2090 -GPSLatitudeRef=N -GPSLongitudeRef=E /tmp/geotagged.jpg
cp /tmp/geotagged.jpg geotagged.jpg
```

Report back: "Lab ready for Q168 — geotagged.jpg with GPS EXIF data ready"
