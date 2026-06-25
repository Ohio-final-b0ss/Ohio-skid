
---

# The Port Scanning Blueprint (Alternative Methodologies)

## 📦 Common Tier (Native Binaries & Protocol Fundamentals)

These tools are either pre-installed on almost every Linux distribution by default or represen>

### 1. Netcat TCP Connection Check (`nc -zv`)

* **The Mechanics:** Attempts a clean, full TCP 3-way handshake via standard OS socket calls. >
* **The Command:**
```bash
nc -zv 192.168.80.128 21 80 443

```


* **Flags:** `-z` sets Zero-I/O mode (scans without sending data payload); `-v` triggers verbo>

### 2. Netcat Sequential Range Sweep

* **The Mechanics:** Feeds a numeric sequence directly into the Netcat engine to crawl an enti>
* **The Command:**
```bash
nc -zv 192.168.80.128 20-100

```



### 3. Native Linux Bash Socket Probing (`/dev/tcp`)

* **The Mechanics:** The ultimate defensive evasion method. It uses no external binaries or ut>
                                      [ Read 153 lines ]
^G Help        ^O Write Out   ^F Where Is    ^K Cut         ^T Execute     ^C Location
^X Exit        ^R Read File   ^\ Replace     ^U Paste       ^J Justify     ^/ Go To Line
