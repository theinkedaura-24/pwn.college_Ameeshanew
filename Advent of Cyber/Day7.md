# Scan-ta Clause

Learn the basics of network service discovery with Nmap, explore core network protocols, and apply your knowledge to regain access to a compromised server by discovering hidden services and exploiting exposed ports.

## Solution:

**Step 1: Starting the Environment**

- Started both the AttackBox and target machine
- Waited approximately 2 minutes for machines to fully boot
- Target: `tbfc-devqa01` QA server at `MACHINE_IP`
- Goal: Scan for open ports, find hidden keys, and regain access

**Step 2: Understanding the Attack Plan**

**Engagement Strategy:**
1. Know your target (tbfc-devqa01 server with known IP)
2. Scan IP for open ports (especially common ones: 22/SSH, 80/HTTP)
3. Explore what's behind the open ports
4. Exploit exposed services to regain access
5. Collect three keys along the way (format: `KEYNAME:KEY`)

**Step 3: Basic Nmap Port Scan**

- Opened terminal on AttackBox
- Ran basic Nmap scan targeting the top 1000 most common ports

```bash
root@attackbox:~# nmap MACHINE_IP
```

**Results:**
```
Nmap scan report for MACHINE_IP
Host is up (0.061s latency).
PORT     STATE SERVICE
22/tcp   open  ssh
80/tcp   open  http
```

**Initial Findings:**
- **Port 22/TCP**: SSH service - requires password for remote access
- **Port 80/TCP**: HTTP web server - accessible via browser

**Step 4: Accessing the Defaced Website**

- Navigated to `http://MACHINE_IP` in browser
- Found defaced website with message: **"Pwned by HopSec"**
- Bad bunnies had compromised the server
- Admin panel locked - need to find keys

![Defaced website showing "Pwned by HopSec"](https://github.com/user-attachments/assets/defaced-website)

**Step 5: Comprehensive Port Scan (All 65535 Ports)**

- Default Nmap scan only checks 1000 common ports
- Total possible ports: **65,535** per protocol (TCP/UDP)
- Added flags: `-p-` (all ports) and `--script=banner` (service identification)

```bash
root@attackbox:~# nmap -p- --script=banner MACHINE_IP
```

**Enhanced Results:**
```
Nmap scan report for MACHINE_IP
Host is up (0.00036s latency).
Not shown: 65531 filtered ports
PORT      STATE SERVICE
22/tcp    open  ssh
|_banner: SSH-2.0-OpenSSH_9.6p1 Ubuntu-3ubuntu13.14
80/tcp    open  http
21212/tcp open  trinket-agent
|_banner: 220 (vsFTPd 3.0.5)
25251/tcp open  unknown
|_banner: TBFC maintd v0.2\x0AType HELP for commands.
```

**New Discoveries:**
- **Port 21212/TCP**: FTP server (vsFTPd 3.0.5) running on non-standard port
- **Port 25251/TCP**: Custom TBFC maintenance application

**Step 6: Accessing FTP Server (Key 1)**

- FTP typically runs on port 21, but here it's on port 21212
- Attempted anonymous FTP login (no password required)

```
root@attackbox:~# ftp MACHINE_IP 21212
Connected to MACHINE_IP.
Name (MACHINE_IP:root): anonymous
331 Please specify the password.
Password: [press Enter for anonymous]
230 Login successful.
```

**Listing FTP Files:**
```
ftp> ls
-rw-r--r--  1  ftp  ftp  13 Oct 22 16:27 tbfc_qa_key1
```

**Retrieving First Key:**
```
ftp> get tbfc_qa_key1 -
[Key displayed to stdout]
```

- **Key 1 Found: `3aster_`**
- Exited FTP: `ftp> quit`

**Step 7: Connecting to Custom TBFC App (Key 2)**

- Port 25251 running unknown custom service
- Not HTTP or FTP - standard clients won't work
- Used **Netcat (nc)** - universal network service client

```
root@attackbox:~# nc -v MACHINE_IP 25251
Connection to MACHINE_IP 25251 port [tcp/*] succeeded!
TBFC maintd v0.2
Type HELP for commands.
```

**Exploring Available Commands:**
```
HELP
Commands: HELP, STATUS, GET KEY, QUIT
```

**Retrieving Second Key:**
```
GET KEY
```

- **Key 2 Found: `15_th3_`**
- Exited Netcat: Pressed `Ctrl+C`

**Step 8: UDP Port Scanning (Key 3)**

- Previous scans only checked **TCP** ports
- Another transport protocol exists: **UDP** (65,535 additional ports)
- Used `-sU` flag to scan UDP ports

```
root@attackbox:~# nmap -sU MACHINE_IP
```

**UDP Results:**
```
PORT   STATE SERVICE
53/udp open  domain
```

**Discovery:**
- **Port 53/UDP**: DNS (Domain Name System) service
- DNS connects domain names to IP addresses
- Potential for hidden information in DNS records

**Step 9: Querying DNS for Hidden Key**

- Used `dig` command for advanced DNS queries
- Queried TXT record type (often used for metadata/verification)
- Syntax: `dig @[DNS_SERVER] [TYPE] [DOMAIN] +short`

```
root@attackbox:~# dig @MACHINE_IP TXT key3.tbfc.local +short
```

**DNS Response:**
- **Key 3 Found: `n3w_xm45`**

**Step 10: Combining Keys to Access Admin Panel**

- Collected all three keys:
  1. `3aster_`
  2. `15_th3_`
  3. `n3w_xm45`
- Combined key: **`3aster_15_th3_n3w_xm45`** (Easter is the new Xmas!)
- Navigated to `http://MACHINE_IP` admin panel
- Entered combined key to unlock Secret Admin Console

![Admin console access](https://github.com/user-attachments/assets/admin-console)

**Step 11: On-Host Service Discovery**

- Now inside the server - no need for external scanning
- Can directly ask OS for listening ports
- Used `ss -tunlp` command (modern replacement for `netstat`)

**Command Breakdown:**
- `-t`: Show TCP sockets
- `-u`: Show UDP sockets  
- `-n`: Show numerical addresses (no DNS resolution)
- `-l`: Show only listening sockets
- `-p`: Show process information

```bash
tbfcapp@tbfc-devqa01:~$ ss -tunlp
```

**Output:**
```
Netid  State   Recv-Q  Send-Q       Local Address:Port      Peer Address:Port  
udp    UNCONN  0       0                  0.0.0.0:53             0.0.0.0:*                               
tcp    LISTEN  0       50                 0.0.0.0:25251          0.0.0.0:*                               
tcp    LISTEN  0       32                 0.0.0.0:21212          0.0.0.0:*                               
tcp    LISTEN  0       4096               0.0.0.0:22             0.0.0.0:*                               
tcp    LISTEN  0       32                 0.0.0.0:53             0.0.0.0:*                               
tcp    LISTEN  0       511                0.0.0.0:80             0.0.0.0:*                               
tcp    LISTEN  0       2048             127.0.0.1:8000           0.0.0.0:*
tcp    LISTEN  0       151              127.0.0.1:3306           0.0.0.0:*                               
tcp    LISTEN  0       4096             127.0.0.1:7681           0.0.0.0:*
```

**Key Observation:**
- Services on `0.0.0.0`: Accessible externally (what we scanned earlier)
- Services on `127.0.0.1`: Only accessible from localhost
- **Port 3306/TCP** on localhost: MySQL database (default port)

**Step 12: Accessing MySQL Database**

- MySQL typically requires password for remote connections
- Often allows unauthenticated localhost connections
- Since we're inside the host, can access directly

**Listing Database Tables:**
```bash
tbfcapp@tbfc-devqa01:~$ mysql -D tbfcqa01 -e "show tables;"
```

**Output:**
```
+--------------------+
| Tables_in_tbfcqa01 |
+--------------------+
| flags              |
+--------------------+
```

**Querying the Flags Table:**
```bash
tbfcapp@tbfc-devqa01:~$ mysql -D tbfcqa01 -e "select * from flags;"
```

- **Final Flag: `THM{4ll_s3rvice5_d1sc0vered}`**

## Flags:

```
Evil message on website: Pwned by HopSec
Key 1 (FTP): 3aster_
Key 2 (TBFC app): 15_th3_
Key 3 (DNS): n3w_xm45
MySQL port: 3306
Final flag: THM{4ll_s3rvice5_d1sc0vered}
```

## Concepts learnt:

- **Network Port**: Virtual communication endpoint identified by number (0-65535) - allows multiple services on one IP address
- **TCP (Transmission Control Protocol)**: Reliable, connection-oriented protocol ensuring data delivery in correct order - used for HTTP, SSH, FTP
- **UDP (User Datagram Protocol)**: Connectionless, faster protocol without delivery guarantees - used for DNS, streaming, gaming
- **Port Scanning**: Process of probing ports to identify which services are accessible - fundamental reconnaissance technique
- **Nmap (Network Mapper)**: Powerful open-source network scanning tool for discovering hosts, services, and vulnerabilities
- **Banner Grabbing**: Technique to identify service versions by capturing welcome messages/banners - helps identify vulnerabilities
- **SSH (Secure Shell)**: Encrypted protocol for remote command-line access - typically port 22
- **HTTP (Hypertext Transfer Protocol)**: Web browsing protocol - typically port 80 (443 for HTTPS)
- **FTP (File Transfer Protocol)**: Protocol for transferring files between systems - typically port 21, but can be configured on any port
- **Anonymous FTP**: FTP configuration allowing access without authentication - common for public file sharing
- **DNS (Domain Name System)**: Hierarchical naming system translating human-readable domains to IP addresses - port 53
- **TXT Record**: DNS record type storing arbitrary text data - often used for verification, SPF records, or hiding information
- **Netcat (nc)**: Swiss Army knife networking utility for reading/writing network connections - useful for custom protocols
- **MySQL**: Popular open-source relational database management system - default port 3306
- **Listening Port**: Port actively accepting incoming connections - indicates running service
- **Localhost (127.0.0.1)**: Loopback address referring to the local machine - services here aren't externally accessible
- **0.0.0.0 Binding**: Listening on all network interfaces - makes service accessible from external networks
- **ss (Socket Statistics)**: Modern Linux utility displaying socket information - replaces older `netstat` command
- **Service Enumeration**: Process of identifying and gathering information about running services and their versions
- **Non-Standard Ports**: Services configured on unusual ports to obscure functionality or avoid automated scanners

## Notes:

- Default Nmap scan only checks **top 1000 common ports** out of 65,535 possible - comprehensive scans require `-p-` flag
- Scanning all ports takes significantly longer than default scan - patience required
- Services can run on **any port**, not just defaults (FTP on 21212 instead of 21)
- **Alternative port scanning tools**: Masscan (faster), Naabu (modern), Rustscan (speed-focused)
- **TCP vs UDP scanning**: UDP scanning is slower and less reliable due to protocol nature
- Could have used `netstat -tunlp` instead of `ss` on older systems
- The `-e` flag in MySQL command executes query and exits without interactive shell
- Alternative MySQL access: Could have used interactive shell with `mysql -D tbfcqa01`
- **Security lesson**: Running services on non-standard ports provides minimal security through obscurity
- **Banner grabbing alternative**: Could manually connect with `nc` and read service banners
- The `dig` command's `+short` flag provides concise output (just the answer)
- **Localhost-only services** are common for databases to prevent unauthorized remote access
- Real-world scenario: Always scan both TCP and UDP - attackers hide services in unexpected places
- **Nmap timing**: Can use `-T4` flag to speed up scans (more aggressive timing)
- The combined key "3aster_15_th3_n3w_xm45" reads as "Easter is the new Xmas" - clever wordplay
- **Port 3306** is instantly recognizable to security professionals as MySQL
- Could have used Nmap's service detection (`-sV`) instead of banner script
- Alternative FTP commands: `mget *` to download all files at once
- The `anonymous` FTP user typically has limited read-only access
- **DNS TXT records** are increasingly used for domain verification (SPF, DKIM, domain ownership)

***
