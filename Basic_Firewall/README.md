# 🔥 Python Firewall Script (Scapy + IPTables)

This project provides a simple Python-based intrusion detection and prevention script using **Scapy** and **iptables**. It monitors incoming traffic in real time and blocks suspicious activity based on:

- A **whitelist** of trusted IPs  
- A **blacklist** of known bad IPs  
- A **BadPacket signature detection** (e.g., Code Red worm)  
- A **packet rate threshold** to detect flooding  

---

## 📦 Features

- 🛡️ Block IPs in a blacklist  
- ✅ Skip IPs in a whitelist  
- 📈 Detect IPs sending \>50 packets/sec  
- 🧪 Detect malicious payloads like `GET /scripts/root.exe`  
- 📁 Log all blocking events with timestamps  

---

## 🧠 How it Works

The script:  
1. Sniffs all incoming IP packets.  
2. Checks if the source IP is whitelisted (ignored if true).  
3. Blocks if it's blacklisted.  
4. Detects suspicious `GET /scripts/root.exe` requests (BadPacket).  
5. Counts packets per IP every second and blocks those above a configurable threshold.  

---

## 🧪 Example Test Scenarios

### 1. 🧪 Windows machine pinging Kali

A Windows machine tries to ping the Kali machine running this script.

- If the Windows IP is in `blacklist.txt`, it is automatically **blocked** by:  
  ```bash
  iptables -A INPUT -s \<ip\> -j DROP
  ```
- The event is logged in `logs/log_*.txt`.
![Blocked ip example](/example.PNG)

---

### 2. 💣 Simulating a BadPacket Attack (Code Red Worm-like)

You can use the following Python script to simulate a malicious payload targeting port 80:

```python
from scapy.all import IP, TCP, Raw, send

def send_Badpacket(target_ip, target_port=80, source_ip="192.168.1.1", source_port=12345):
    packet = (
        IP(src=source_ip, dst=target_ip)
        / TCP(sport=source_port, dport=target_port)
        / Raw(load="GET /scripts/root.exe HTTP/1.0\r\nHost: example.com\r\n\r\n")
    )
    send(packet)

if __name__ == "__main__":
    target_ip = "192.168.xxx.xxx"  # Replace with your Kali IP
    send_Badpacket(target_ip)
```

✅ If the script detects this signature, it blocks the IP and logs it as a **BadPacket** attack.

---

## ⚙️ Configuration

- **Whitelist file:** `whitelist.txt`  
- **Blacklist file:** `blacklist.txt`  
- **Threshold:** 50 packets/sec (can be adjusted via `THRESHOLD` constant)  
- **Log folder:** `logs/`  

---

## 🚀 Running the Script

```bash
sudo python3 firewall.py
```

Make sure to run as root for `iptables` to work.

---


## 🛠 Requirements

- Python 3  
- Scapy (`pip install scapy`)  
- iptables  

---

