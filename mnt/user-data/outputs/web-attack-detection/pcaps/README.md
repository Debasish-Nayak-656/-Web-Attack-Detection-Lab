# PCAP Files

This folder stores Wireshark packet captures from the lab.

## Files to add here:

| File | Description |
|------|-------------|
| `normal_traffic.pcapng` | Baseline capture — normal browser activity on DVWA |
| `attack_traffic.pcapng` | Full attack capture — Nmap, SQLi, XSS, and Gobuster sessions |

## How to capture:

```bash
# Start Wireshark on loopback interface
sudo wireshark &
# Select: lo (loopback)
# Start capture, run your attacks, then stop and save as .pcapng
```

> Note: .pcapng files can be large. Consider compressing before uploading to GitHub, or using Git LFS for files over 50MB.
