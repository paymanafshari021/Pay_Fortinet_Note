```bash
diagnose sniffer packet <interface> '<filter>' <verbose> <count> <tsformat>
diag sniffer packet any 'port 67 or port 68' 4
diagnose sniffer packet any "port 541 and host <FortiManager_IP>" 6 0 a
diagnose sniffer packet any 'host 10.200.1.10 and host 10.200.2.10 and icmp' 4 0 a
diagnose sniffer packet any 'port 80 and host 172.20.120.18' 4 0 a
diagnose sniffer packet any 'src host 192.168.10.1 and port (80 or 443 or 8080)' 4 0 a
```
#### Command Syntax
```bash
diagnose sniffer packet <interface> '<filter>' <verbose> <count> <tsformat>
```
- **`<interface>`**: Interface to sniff (e.g., `port1`, `wan1`, or `any` for all interfaces).
- **`<filter>`**: Filter to specify traffic (e.g., `host 8.8.8.8`, `port 80`, or `none` for all packets). Use single quotes.
- **`<verbose>`**: Verbosity level (1–6):
  - **1**: Packet header.
  - **2**: Header + IP data.
  - **3**: Header + Ethernet data.
  - **4**: Header + interface name.
  - **5**: Header + IP data + interface name.
  - **6**: Header + Ethernet data + interface name (convertible to PCAP for Wireshark).
- **`<count>`**: Number of packets to capture (0 for unlimited, stop with `Ctrl+C`).
- **`<tsformat>`**: Timestamp format:
  - `a`: Absolute UTC time (yyyy-mm-dd hh:mm:ss.ms).
  - `l`: Local time (yyyy-mm-dd hh:mm:ss.ms).
  - (blank): Relative to start of capture (ss.ms).