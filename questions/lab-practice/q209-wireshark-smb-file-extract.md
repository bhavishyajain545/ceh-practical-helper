# Q209 — Extract File Transferred Over SMB

| Field | Value |
|-------|-------|
| **Target** | local |
| **Domain** | Sniffing / Packet Analysis |
| **Difficulty** | 🔴 Hard |
| **Tools** | `wireshark, tshark` |
| **Time budget** | 10–15 min |

---

## 📝 Question

"Analyze `smb_transfer.pcap`. A file was transferred over SMB. Identify the filename and export the file from the pcap. Report the filename and the flag inside it."

---

## 🎯 Flag Format

```
filename=<name>; flag=<string>
```

---

## 💡 Hints

**Hint 1**

Wireshark: File → Export Objects → SMB

**Hint 2**

Filter: `smb2` → look for Create Request/Response to find filenames.

---

## ✅ Solution

Wireshark: File → Export Objects → SMB → Save All
```bash
tshark -r smb_transfer.pcap --export-objects smb,smb_export/
ls smb_export/
cat smb_export/secret.txt
```

**Answer:** `filename=secret.txt; flag=SMB_FILE_CAPTURED`

---

## 🤖 Claude Setup Prompt

```bash
echo "SMB_FILE_CAPTURED" > /tmp/secret.txt
tcpdump -i eth0 port 445 -w smb_transfer.pcap &
smbclient //192.168.52.129/tmp -N -c "put /tmp/secret.txt secret.txt"
sleep 3 && kill %1
```

Report back: "Lab ready for Q209 — smb_transfer.pcap with SMB file transfer ready"
