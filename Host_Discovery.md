---

### 🟢 COMMON TIER (The Daily Drivers)

These are your bread-and-butter techniques. They are standard, highly accurate, and fast. You will use these on almost every single penetration testing engagement or exam lab.

* **1. ARP-Scan (`arp-scan`)**
* *Rarity:* Common
* *How it works:* Broadcasts raw Address Resolution Protocol (ARP) requests to every IP in a local subnet range.
* *Why it's used:* It pierces through host-level software firewalls (like Windows Firewall) because local network devices *must* respond to ARP to communicate on a local area network (LAN).
* *Syntax:* `sudo arp-scan --interface=ens33 --localnet`


* **2. Active Netdiscover (`netdiscover -r`)**
* *Rarity:* Common
* *How it works:* Actively injects ARP requests into a network segment and builds a live, auto-refreshing UI table of active hosts.
* *Why it's used:* Great for visual organization; it maps hardware MAC vendors to tell you instantly if a target is a "VirtualBox" or "VMware" instance.
* *Syntax:* `sudo netdiscover -i ens33 -r 192.168.80.0/24`


* **3. Nmap Ping Sweep (`nmap -sn`)**
* *Rarity:* Common
* *How it works:* Discovers live hosts using a combination of ICMP echo requests (pings) and TCP ACK packets across a subnet.
* *Why it's used:* It is the gold standard for structural baseline scanning. The `-sn` flag ensures Nmap checks if a host is alive and then immediately stops before wasting time probing ports.
* *Syntax:* `nmap -sn 192.168.80.0/24`



---

### 🔵 RARE TIER (The Tactical Pivots)

You don't pull these out every day, but when a standard tool fails because a network doesn't have common tools installed or because basic ICMP traffic is blocked, these tactical pivots save the day.

* **4. Simultaneous Bash Ping Loop (Living off the Land)**
* *Rarity:* Rare
* *How it works:* Uses a native Bash `for` loop to send standard ICMP pings to an entire subnet range all at the exact same second by sending the processes to the system background (`&`).
* *Why it's used:* Used when you break into a machine and find that `nmap`, `arp-scan`, and `netdiscover` are missing and you have no internet access to install them. You use the operating system's built-in tools.
* *Syntax:* `for i in {1..254}; do ping -c 1 -W 1 192.168.80.$i | grep "from" & done`


* **5. Fping Subnet Sweep (`fping`)**
* *Rarity:* Rare
* *How it works:* An optimized version of the traditional administrative `ping` command meant specifically for scanning massive blocks of parallel hosts.
* *Why it's used:* It scales much cleaner than a custom Bash script and outputs a clean, line-by-line list of live IPs that is perfect for piping directly into custom automation scripts.
* *Syntax:* `fping -g 192.168.80.0/24 -a 2>/dev/null`



---

### 🟣 MYTHIC TIER (The Ghost Protocol)

These techniques are specialized, incredibly stealthy, or bypass standard network constraints entirely. They represent a deeper, advanced understanding of underlying network layers.

* **6. Neighbor Cache Inspection (`ip neigh`)**
* *Rarity:* Mythic
* *How it works:* Queries your own Linux kernel's internal memory space (`arp -a` or `ip neigh`) to see what local devices your machine has already interacted with recently.
* *Why it's used:* It is **100% passive and completely invisible**. It sends **zero** network packets out into the wild, meaning an Intrusion Detection System (IDS) cannot detect you looking. You are harvesting data already sitting on your own hardware.
* *Syntax:* `ip neigh`


* **7. Passive Netdiscover Sniffing (`netdiscover -p`)**
* *Rarity:* Mythic
* *How it works:* Forces your network adapter into a pure listening state, silently capturing background broadcast packets moving through the air or across a switched network without transmitting a single byte of data.
* *Why it's used:* Pure, untraceable reconnaissance. If a target machine talks to the gateway, you intercept it out of thin air, but it never knows Ohio is watching.
* *Syntax:* `sudo netdiscover -p`


* **8. IPv6 Multicast Discovery (`ping6`)**
* *Rarity:* Mythic
* *How it works:* Bypasses the IPv4 protocol completely. It transmits a single packet to a universal IPv6 link-local "all-nodes" multicast address (`ff02::1`).
* *Why it's used:* By law of the IPv6 protocol, every network card active on that link segment *must* reply with its unique IPv6 address. It completely bypasses standard IPv4 firewall rules and reveals hidden targets in a single shot.
* *Syntax:* `sudo ping6 -I ens33 ff02::1`
---
