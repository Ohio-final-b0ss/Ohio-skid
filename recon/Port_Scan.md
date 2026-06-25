
---

# The Port Scanning Blueprint (Alternative Methodologies)

## 📦 Common Tier (Native Binaries & Protocol Fundamentals)

These tools are either pre-installed on almost every Linux distribution by default or represent the baseline protocols required to find open gates.

### 1. Netcat TCP Connection Check (`nc -zv`)

* **The Mechanics:** Attempts a clean, full TCP 3-way handshake via standard OS socket calls. Highly reliable but completely visible in target application logs.
* **The Command:**
```bash
nc -zv 192.168.80.128 21 80 443

```


* **Flags:** `-z` sets Zero-I/O mode (scans without sending data payload); `-v` triggers verbosity.

### 2. Netcat Sequential Range Sweep

* **The Mechanics:** Feeds a numeric sequence directly into the Netcat engine to crawl an entire range of ports one by one.
* **The Command:**
```bash
nc -zv 192.168.80.128 20-100

```



### 3. Native Linux Bash Socket Probing (`/dev/tcp`)

* **The Mechanics:** The ultimate defensive evasion method. It uses no external binaries or utilities at all. It relies entirely on the internal implementation of the native Bash shell to open raw file descriptors against network sockets.
* **The Command:**
```bash
(timeout 1 bash -c "echo > /dev/tcp/192.168.80.128/80") 2>/dev/null && echo "Port 80: OPEN" || echo "Port 80: CLOSED"

```



### 4. The Blind Spot: UDP Port Scanning (`nmap -sU`)

* **The Mechanics:** TCP requires a handshake, but UDP is entirely connectionless. To find a UDP port (like DNS on 53 or SNMP on 161), Nmap sends an empty UDP packet. If the port is *closed*, the target operating system's kernel drops an **ICMP Port Unreachable** packet back. If the port is *open*, the target stays completely silent, or replies with app-specific data.
* **The Command:**
```bash
sudo nmap -sU -p 53,161 192.168.80.128

```



---

## 🔮 Rare Tier (Asynchronous Velocity & Modern Engines)

These tools break away from traditional linear scanning logic. They are built for modern bug hunting pipelines where speed across massive IP blocks is required.

### 5. RustScan Async Engine (`rustscan`)

* **The Mechanics:** Built in Rust, this tool uses a highly multi-threaded asynchronous engine to scan all 65,535 ports on a machine in less than 3 seconds. It functions as a "scout"—it instantly identifies open ports, then automatically pipes those exact numbers directly into Nmap for deep version detection.
* **The Command:**
```bash
rustscan -a 192.168.80.128 --ulimit 5000 -- -sV

```



### 6. Naabu Asset Discovery (`naabu`)

* **The Mechanics:** A lightweight, modern scanner written in Go by ProjectDiscovery. It uses raw sockets to perform fast, stateless SYN scans, optimized to handle massive multi-host asset lists without clogging network routers.
* **The Command:**
```bash
naabu -host 192.168.80.128 -p 21,80,443

```



### 7. Masscan Kernel-Bypass Sweep (`masscan`)

* **The Mechanics:** Uses a custom asynchronous network driver stack that communicates directly with your network card, completely bypassing the Linux kernel's internal TCP/IP overhead. It can transmit packets at millions of frames per second.
* **The Command:**
```bash
sudo masscan -p21,80,443 192.168.80.0/24 --rate=1000

```



---

## 🦄 Mythic Tier (Custom Packet Crafting & Header Forgery)

These methods construct packets manually bit-by-bit to trick firewalls or exploit edge cases in how network operating systems handle weird inputs.

### 8. Hping3 Custom SYN Forgery (`hping3`)

* **The Mechanics:** A dedicated command-line packet assembler. Instead of relying on an automated tool's signature, you craft raw TCP headers manually to test specific ports or simulate highly tailored traffic patterns.
* **The Command:**
```bash
sudo hping3 -S -p 80 -c 1 192.168.80.128

```


* **Flags:** `-S` manually sets the SYN flag bit; `-c 1` tells it to fire exactly one packet. You read the incoming flags to determine the port state.

### 9. Inverse TCP Flag Evasion: The Xmas Scan (`nmap -sX`)

* **The Mechanics:** Sends a bizarre, illegal TCP packet with three specific flags switched on simultaneously: `FIN` (Finish), `PSH` (Push), and `URG` (Urgent). According to internet protocol design rules (RFC 793), if a port is closed, it *must* reply with an `RST`. If it is open, it will simply drop the packet into the trash without responding.
* **The Command:**
```bash
sudo nmap -sX 192.168.80.128

```


* **Tactical Use:** It lights the packet header up "like a Christmas tree." It is highly effective against older stateless firewalls or Unix systems, though Windows targets will ignore it entirely due to a different kernel configuration.

### 10. Scapy Raw Python Injector (`scapy`)

* **The Mechanics:** Drops into a Python shell environment where you build custom programmatic packets by layering protocol properties like lego bricks, allowing you to manipulate fields like Window Size or TTL to bypass security controls.
* **The Code Execution:**
```bash
sudo python3 -c "from scapy.all import *; reply = sr1(IP(dst='192.168.80.128')/TCP(dport=80,flags='S'),timeout=1); reply.show() if reply else print('Filtered')"

```



---

### The Tactical Alternative Matrix

| Tier | Technique / Tool | Transport Protocol | Tactical Use Case |
| --- | --- | --- | --- |
| **Common** | `nc -zv` | TCP | Testing connection parameters quickly without root access. |
| **Common** | `/dev/tcp` | TCP | Living-off-the-land reconnaissance on highly secure, restricted shells. |
| **Common** | `nmap -sU` | UDP | Auditing overlooked background services (DNS, SNMP, DHCP). |
| **Rare** | `rustscan` | TCP | Blasting through all 65,535 ports on a single host in under 5 seconds. |
| **Rare** | `naabu` | TCP | Fast, scriptable port enumeration across vast cloud subnets. |
| **Rare** | `masscan` | TCP / UDP | Extreme scale reconnaissance spanning full corporate netblocks. |
| **Mythic** | `hping3` | Custom TCP | Manual firewall analysis and precise packet-injection testing. |
| **Mythic** | `nmap -sX` | Custom TCP | Bypassing legacy packet filters by using illegal flag states. |
| **Mythic** | `scapy` | Full Stack | Programmatic exploitation testing and custom scanner development. |

---

