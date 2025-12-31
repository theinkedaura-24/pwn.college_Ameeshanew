# C2 Detection: Command & Carol

Explore how to analyze large PCAP files and detect Command & Control traffic using RITA and Zeek logs.

## Solution:

- Started by understanding the story: TBFC is hunting for Command and Control (C2) traffic using RITA (Real Intelligence Threat Analytics)
- Need to convert PCAP files to Zeek logs and use RITA to analyze potential C2 communications
- Goal is to identify malicious communication patterns from King Malhare's infrastructure

### Step 1: Understanding RITA and Zeek

- **RITA (Real Intelligence Threat Analytics)**: Open-source framework for detecting C2 communication by analyzing network traffic
- **Key RITA Features**:
  - C2 beacon detection
  - DNS tunneling detection
  - Long connection detection
  - Data exfiltration detection
  - Threat intel feed checking
  - Connection severity scoring

- **Zeek**: Open-source network security monitoring (NSM) tool that converts PCAPs into structured logs
- RITA only accepts Zeek logs as input, not raw PCAPs

![RITA Overview](https://i.imgur.com/placeholder1.png)

### Step 2: Exploring the Target Machine

- Started the target machine and opened terminal
- Navigated to home directory and listed contents:

```
ubuntu@tryhackme$ ls
Desktop  Documents  Downloads  Music  Pictures  Public  Templates  Videos  pcaps  zeek_logs
```

- Found two important directories:
  - `pcaps/`: Contains packet capture files
  - `zeek_logs/`: Contains Zeek-generated logs

![Directory Structure](https://i.imgur.com/placeholder2.png)

### Step 3: Converting PCAP to Zeek Logs (Practice Run)

- First practiced with the AsyncRAT example PCAP
- Used Zeek to convert PCAP to structured logs:

```
ubuntu@tryhackme$ zeek readpcap pcaps/AsyncRAT.pcap zeek_logs/asyncrat
Starting the Zeek docker container
Zeek logs will be saved to /home/ubuntu/zeek_logs/asyncrat
```

- Navigated to output directory and examined generated logs:

```
ubuntu@tryhackme$ cd /home/ubuntu/zeek_logs/asyncrat/ && ls
capture_loss.log  dns.log    http.log         known_services.log  notice.log  packet_filter.log  software.log  stats.log  x509.log
conn.log          files.log  known_hosts.log  loaded_scripts.log  ocsp.log    reporter.log       ssl.log       weird.log
```

**Key Zeek log types generated:**
- `conn.log`: Connection data
- `dns.log`: DNS queries
- `http.log`: HTTP traffic
- `ssl.log`: SSL/TLS connections

![Zeek Logs Generated](https://i.imgur.com/placeholder3.png)

### Step 4: Importing Zeek Logs into RITA

- Imported the Zeek logs into RITA for analysis:

```bash
ubuntu@tryhackme$ rita import --logs ~/zeek_logs/asyncrat/ --database asyncrat
[REDACTED]
2025-10-23T10:56:58Z INF Initiating new import... dataset=asyncrat directory=/tmp/zeek_logs
2025-10-23T10:56:58Z INF [THREAT INTEL] Updating online feed... feed_url=https://feodotracker.abuse.ch/downloads/ipblocklist.txt
[-] Parsing:  /tmp/zeek_logs/conn.log
[-] Parsing:  /tmp/zeek_logs/http.log
[-] Parsing:  /tmp/zeek_logs/ssl.log
[-] Parsing:  /tmp/zeek_logs/dns.log
Log Parsing ✓ ████████████████████ 4 / 4
```

![RITA Import Process](https://i.imgur.com/placeholder4.png)

### Step 5: Viewing RITA Analysis Results

- Viewed the analyzed results:

```bash
ubuntu@tryhackme$ rita view asyncrat
```

- RITA displayed a structured terminal interface with three panes:
  1. **Search bar** (activated with `/`)
  2. **Results pane** (showing connections)
  3. **Details pane** (showing threat modifiers)

![RITA Interface](https://i.imgur.com/placeholder5.png)

**Results Pane Columns:**
- **Severity**: Score based on threat modifiers
- **Source and Destination**: IP/FQDN information
- **Beacon Likelihood**: Probability of C2 beacon activity
- **Duration**: Connection duration (long = suspicious)
- **Subdomains**: Number of unique subdomains
- **Threat Intel**: Matches on threat feeds

### Step 6: Understanding Threat Modifiers

- Examined the Details Pane showing two categories:

**Threat Modifiers:**
- **MIME type/URI mismatch**: Suspicious header mismatches
- **Rare signature**: Unusual connection patterns
- **Prevalence**: Number of internal hosts communicating with external host
- **First Seen**: Date external host first appeared
- **Missing host header**: HTTP connections without host header
- **Large outgoing data**: Potential data exfiltration
- **No direct connections**: Hidden C2 communication

**Connection Info:**
- **Connection count**: Number of connections (high = beacon activity)
- **Total bytes sent**: Data volume (high = exfiltration)
- **Port/Protocol/Service**: Non-standard ports warrant investigation

![Threat Modifiers](https://i.imgur.com/placeholder6.png)

### Step 7: Analyzing Practice Results

- Found suspicious entry: `sunshine-bizrate-inc-software[.]trycloudflare[.]com`
- Second entry: IP `91[.]134[.]150[.]150`
- Both flagged as malicious on VirusTotal
- Notable indicators:
  - Rare signature modifier present
  - Long connection duration
  - Non-standard ports
  - Unique TLS handshake patterns

![Suspicious Entries](https://i.imgur.com/placeholder7.png)

### Step 8: Challenge - Converting Challenge PCAP

- Converted the challenge PCAP to Zeek logs:

```bash
ubuntu@tryhackme$ zeek readpcap ~/pcaps/rita_challenge.pcap zeek_logs/challenge
Starting the Zeek docker container
Zeek logs will be saved to /home/ubuntu/zeek_logs/challenge
```

![Challenge Conversion](https://i.imgur.com/placeholder8.png)

### Step 9: Importing Challenge into RITA

- Imported challenge Zeek logs into RITA database named "test":

```bash
ubuntu@tryhackme$ rita import --logs ~/zeek_logs/challenge/ --database test
[REDACTED]
Log Parsing ✓ ████████████████████ 4 / 4
```

![Challenge Import](https://i.imgur.com/placeholder9.png)

### Step 10: Analyzing Challenge Results

- Viewed RITA analysis:

```
ubuntu@tryhackme$ rita view test
```

![RITA Challenge View](https://i.imgur.com/placeholder10.png)

### Step 11: Answering Questions - Manual Analysis

**Question 1: Hosts communicating with malhare.net**
- Scrolled through results pane
- Looked for entries with `malhare.net` as destination
- Counted unique source IPs manually
- Found **6 unique internal hosts**

![Malhare.net Connections](https://i.imgur.com/placeholder11.png)

**Answer 1:** `6`

**Question 2: Threat Modifier for host count**
- Examined details pane on the right
- Found threat modifier section listing "Prevalence"
- Prevalence shows: "X% of hosts communicated with this destination"

![Prevalence Modifier](https://i.imgur.com/placeholder12.png)

**Answer 2:** `prevalence`

**Question 3: Highest connection count to rabbithole.malhare.net**
- Navigated through results looking for `rabbithole.malhare.net`
- Checked "Connection Info" in details pane for each entry
- Found maximum connection count of **40**

![Connection Count](https://i.imgur.com/placeholder13.png)

**Answer 3:** `40`

### Step 12: Using RITA Search Functionality

**Question 4: Search filter construction**
- Pressed `/` to enter search mode
- Pressed `?` to view search help and available fields
- Constructed filter for:
  - Destination: `rabbithole.malhare.net`
  - Beacon score: `>=70%`
  - Sort by duration: descending

![RITA Search Help](https://i.imgur.com/placeholder14.png)

**Search syntax:**
```
dst:rabbithole.malhare.net beacon:>=70 sort:duration-desc
```

- Entered the search filter and verified results

![Search Filter Applied](https://i.imgur.com/placeholder15.png)

**Answer 4:** `dst:rabbithole.malhare.net beacon:>=70 sort:duration-desc`

**Question 5: Port used by 10.0.0.13**
- Searched for source IP `10.0.0.13` communicating with `rabbithole.malhare.net`
- Selected the entry with arrow keys
- Examined "Connection Info" in details pane
- Found port number listed as **80**

![Port Information](https://i.imgur.com/placeholder16.png)

**Answer 5:** `80`

## Flag:

```
No flag for this challenge - focus on C2 detection methodology
```

## Concepts learnt:

- **RITA (Real Intelligence Threat Analytics)**: Open-source framework that analyzes Zeek logs to detect C2 beacons, DNS tunneling, long connections, and data exfiltration through correlation of network metadata

- **Zeek (formerly Bro)**: Network security monitoring tool that converts raw PCAP files into structured, enriched logs containing transaction data and extracted content for threat hunting and incident response

- **C2 Beacon Detection**: Identifying periodic, automated communication between compromised hosts and attacker infrastructure through pattern analysis of connection intervals, frequency, and consistency

- **PCAP to Zeek Conversion**: Using `zeek readpcap` command to parse packet captures into organized log files (conn.log, dns.log, http.log, ssl.log) for structured analysis

- **Threat Modifiers**: Scoring criteria in RITA that determine severity/likelihood of threats - includes prevalence, rare signatures, first seen dates, MIME/URI mismatches, and unusual data volumes

- **Prevalence Analysis**: Examining how many internal hosts communicate with external destinations - low prevalence (few hosts) can indicate targeted attacks rather than legitimate services

- **Long Connection Duration**: Indicator of compromise where connections remain open much longer than typical stateless protocols (HTTP, DNS), suggesting persistent C2 channels like SSH/RDP tunnels

- **Network Security Monitoring (NSM)**: Passive observation and analysis of network traffic to detect threats through behavioral analysis rather than signature-based detection

- **TLS Handshake Analysis**: Identifying malicious encrypted traffic through metadata patterns - malware often creates unique SSL/TLS handshake signatures different from legitimate browsers

- **RITA Search Syntax**: Filtering capabilities using field:value pairs (dst:, beacon:, sort:) to quickly narrow down suspicious connections in large datasets

## Notes:

- **Dataset size matters significantly**: Smaller datasets are more prone to false positives and may not provide comprehensive insights - the challenge dataset was intentionally small but sufficient for learning

- **Key workflow for C2 detection:**
  1. Convert PCAP to Zeek logs using `zeek readpcap`
  2. Import Zeek logs into RITA using `rita import`
  3. Analyze results with `rita view`
  4. Use search filters to narrow suspicious activity
  5. Validate findings with threat intel (VirusTotal, etc.)

- **RITA doesn't require deep Zeek log knowledge** - the framework abstracts complexity and presents actionable intelligence through correlation and scoring

- **Critical indicators to watch for:**
  - Long FQDNs (especially with random characters)
  - Non-standard ports (not 80, 443, 53, 22)
  - High connection counts to single destination
  - Long-duration connections
  - Rare SSL/TLS signatures
  - Low prevalence external hosts
  - Missing or unusual HTTP headers

- **Real-world PCAP source**: Challenge used samples from Bradly Duncan's blog - a valuable resource for malware traffic analysis containing real-world incident captures

- **Threat Intel integration**: RITA automatically cross-references connections against public feeds (Feodo Tracker IP blocklist) - always validate suspicious entries on VirusTotal, AbuseIPDB, or similar platforms

- **Navigation tips:**
  - Use arrow keys to move between entries
  - Press `/` for search mode
  - Press `?` in search for help
  - Press `Esc` to exit search
  - Details pane updates automatically when selecting entries

- **Limitations encountered:**
  - "First Seen" modifier less reliable in small timeframe captures
  - May need to pivot to raw Zeek logs or PCAP for deeper investigation
  - Some PCAPs contain live malicious infrastructure - handle carefully

- **Alternative tools considered:**
  - Wireshark for manual PCAP analysis
  - Suricata/Snort for signature-based IDS
  - NetworkMiner for host/flow analysis
  - Security Onion for full NSM stack
