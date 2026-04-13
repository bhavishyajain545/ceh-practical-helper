# 🔄 Converters & Encoders (Online)

> Quick base/encoding/format conversions. CyberChef does most of these but here are the focused tools.

## The universal converter

**CyberChef** — https://gchq.github.io/CyberChef/ — covers 95% of conversions. Bookmark this first.

---

## Base / number conversions

| From → To | Tool |
|---|---|
| Hex ↔ ASCII | https://www.rapidtables.com/convert/number/hex-to-ascii.html |
| Binary ↔ Decimal ↔ Hex | https://www.rapidtables.com/convert/number/binary-to-decimal.html |
| Decimal ↔ Hex | https://www.binaryhexconverter.com/ |
| ASCII ↔ Decimal | https://www.rapidtables.com/convert/number/ascii-to-decimal.html |

---

## Base64 / Base32

| Tool | URL |
|---|---|
| Base64 Decode/Encode | https://www.base64decode.org/ |
| Base64 → File | https://base64.guru/converter/decode/file |
| File → Base64 | https://base64.guru/converter/encode/file |
| Base32 Decode | https://emn178.github.io/online-tools/base32_decode.html |
| Base58 (Bitcoin) | https://appdevtools.com/base58-encoder-decoder |

---

## URL encoding

| Tool | URL |
|---|---|
| URL Decoder/Encoder | https://www.urldecoder.org/ |
| Multi-pass URL | https://www.urlencoder.io/ |

---

## HTML entities

| Tool | URL |
|---|---|
| HTML Entity decoder | https://mothereff.in/html-entities |
| HTML decode/encode | https://www.freeformatter.com/html-escape.html |

---

## JSON / XML / YAML

| Tool | URL | Use |
|---|---|---|
| JSON Formatter | https://jsonformatter.org/ | Pretty print + validate |
| JSON ↔ YAML | https://www.json2yaml.com/ | Format swap |
| XML Formatter | https://www.freeformatter.com/xml-formatter.html | Pretty + validate |
| JSON Path Tester | https://jsonpath.com/ | Test JSONPath queries |

---

## IP / network

| Tool | URL |
|---|---|
| IP ↔ Decimal | https://www.silisoftware.com/tools/ipconverter.php |
| CIDR Calculator | https://www.subnet-calculator.com/cidr.php |
| MAC Address Lookup (OUI) | https://aruljohn.com/mac/ |

**Useful trick:** Convert `10.10.10.5` to decimal `168430085` — sometimes needed for SSRF bypasses or Linux raw socket questions.

---

## Time / date

| Tool | URL | Use |
|---|---|---|
| Epoch Converter | https://www.epochconverter.com/ | Unix timestamp ↔ date |
| Time Zone Converter | https://www.timeanddate.com/worldclock/converter.html | Forensics on logs |

---

## Image converters

| Tool | URL | Use |
|---|---|---|
| Online Image Converter | https://convertio.co/ | Any → any image format |
| ImageMagick Online | https://imagemagick.org/ | (CLI tool reference) |
| QR Code Decoder | https://zxing.org/w/decode.jspx | Read QR from image |
| QR Code Generator | https://www.qr-code-generator.com/ | Make QR from text |

---

## Text / regex

| Tool | URL | Use |
|---|---|---|
| Regex101 | https://regex101.com/ | Test/build regex with explanation |
| RegExr | https://regexr.com/ | Visual regex builder |
| Diff Checker | https://www.diffchecker.com/ | Compare two texts |

---

## Compression / decompression

| Tool | URL | Use |
|---|---|---|
| Online Unzip | https://extract.me/ | Most archive formats |
| 7-Zip Online | https://www.ezyzip.com/ | ZIP only |

---

## ⚠️ Gotchas

- **CyberChef one-stop:** if doubt, paste in CyberChef's "Magic" recipe, it'll auto-detect
- **Trailing whitespace/newlines** ruin many decoders — strip first
- **Privacy:** for CEH lab content this is fine; never paste prod secrets into online tools
- **CTRL+F is your friend** — most converter sites are full of ads, navigate fast
