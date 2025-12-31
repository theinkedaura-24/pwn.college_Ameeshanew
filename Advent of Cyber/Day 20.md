# Race Conditions: Toy to The World

Learn how to exploit a race condition attack to oversell the limited-edition SleighToy.

## Solution:

- Started by understanding the story: TBFC launched a limited-edition SleighToy with only 10 units, but multiple customers received confirmation emails due to a timing flaw exploited by Bandit Bunnies
- Need to investigate and replicate this race condition vulnerability using Burp Suite

### Step 1: Understanding Race Conditions

- **Race Condition**: Occurs when two or more actions happen simultaneously, and the system's outcome depends on the order in which they finish
- Three main types covered:
  - **TOCTOU (Time-of-Check to Time-of-Use)**: Data changes between check and use
  - **Shared Resource**: Multiple users modify same data simultaneously without proper control
  - **Atomicity Violation**: Process parts run separately, allowing interruptions

### Step 2: Environment Configuration

- Started AttackBox and target machine
- Configured Firefox to route traffic through Burp Suite:
  - Clicked FoxyProxy icon in Firefox
  - Selected Burp profile

![FoxyProxy Configuration](https://i.imgur.com/placeholder1.png)

- Launched Burp Suite from Desktop:
  - Selected "Temporary project in memory"
  - Clicked "Start Burp" with default configuration

![Burp Suite Startup](https://i.imgur.com/placeholder2.png)

- **Critical Step**: Turned off "Intercept" in Burp Suite (Proxy tab → Intercept sub-tab → clicked "Intercept on" to change to "Intercept off")

![Intercept Off](https://i.imgur.com/placeholder3.png)

### Step 3: Making a Legitimate Request

- Navigated to `http://MACHINE_IP` in Firefox
- Logged in with provided credentials:
  - Username: `attacker`
  - Password: `attacker@123`

![Login Page](https://i.imgur.com/placeholder4.png)

- Viewed dashboard showing SleighToy Limited Edition with only 10 units available
- Made legitimate purchase:
  - Clicked "Add to Cart" for SleighToy
  - Clicked "Checkout"
  - Clicked "Confirm & Pay"
- Received success confirmation message

![Checkout Success](https://i.imgur.com/placeholder5.png)

### Step 4: Capturing the Request in Burp Suite

- Switched to Burp Suite
- Navigated to Proxy → HTTP history
- Found the POST request to `/process_checkout` endpoint
- Right-clicked the request → "Send to Repeater"

![Send to Repeater](https://i.imgur.com/placeholder6.png)

### Step 5: Preparing the Race Condition Attack

- Switched to Repeater tab
- Right-clicked on the first tab → "Add tab to group" → "Create tab group"
- Named the tab group "cart"

![Create Tab Group](https://i.imgur.com/placeholder7.png)

- Right-clicked the request tab → "Duplicate tab"
- Entered 15 as the number of copies

![Duplicate Tabs](https://i.imgur.com/placeholder8.png)

### Step 6: Executing the Parallel Requests

- Used Repeater toolbar → Send dropdown menu
- Selected "Send group in parallel (last-byte sync)"
  - This launches all copies at once, maximizing timing overlap to trigger race conditions

![Send Parallel](https://i.imgur.com/placeholder9.png)

- Clicked "Send group (parallel)"
- All 15 requests launched simultaneously to the server

```
POST /process_checkout HTTP/1.1
Host: MACHINE_IP
Content-Type: application/x-www-form-urlencoded
Cookie: session=<session_cookie>

[Request body with cart data]
```

### Step 7: Verification - SleighToy Limited Edition

- Returned to the web application
- Observed multiple confirmed orders
- **SleighToy stock went negative** (exploited successfully!)
- Flag appeared on the page

![Negative Stock and Flag](https://i.imgur.com/placeholder10.png)

### Step 8: Repeating for Bunny Plush (Blue)

- Repeated the exact same process for Bunny Plush (Blue):
  - Added Bunny Plush to cart
  - Made legitimate checkout
  - Captured POST request in Burp Suite
  - Created tab group with 15 duplicates
  - Sent parallel requests
- Stock went negative
- Second flag obtained

![Bunny Plush Flag](https://i.imgur.com/placeholder11.png)

## Flag:

```
THM{WINNER_OF_R@CE007}
```

**Second Flag (Bunny Plush Blue):**
```
THM{WINNER_OF_BUNNY_R@CE}
```

## Concepts learnt:

- **Race Condition Vulnerability**: A timing-based security flaw where multiple simultaneous requests can bypass validation checks due to lack of proper synchronization in the application logic

- **TOCTOU (Time-of-Check to Time-of-Use)**: Specific type of race condition where a system checks a condition (e.g., stock availability) and then performs an action (e.g., order confirmation), but the data changes between these two steps

- **Burp Suite Repeater**: Tool that allows capturing and replaying HTTP requests, including sending multiple identical requests in parallel to exploit timing vulnerabilities

- **Parallel Request Attack**: Technique of sending multiple identical requests simultaneously to overwhelm application logic and bypass validation checks (stock checks, transaction limits, etc.)

- **Last-Byte Synchronization**: Burp Suite feature that holds back the final byte of each request until all requests are ready, then releases them simultaneously to maximize timing overlap

- **Atomic Database Transactions**: Operations that should execute as a single, indivisible unit - either all steps complete successfully or none do, preventing partial updates and race conditions

- **Idempotency Keys**: Unique identifiers assigned to critical operations (like checkouts) to ensure that duplicate requests don't result in duplicate processing

- **Negative Stock Inventory**: Result of race condition exploitation where more items are sold than available, causing inventory to go below zero

## Notes:

- The key vulnerability is that the application checks stock availability and then processes the order in separate non-atomic operations
- Between the check and the update, multiple requests can slip through if timed correctly
- The "last-byte sync" feature in Burp Suite is crucial - it ensures all requests arrive at nearly the same microsecond, maximizing the chance of hitting the race condition window
- Initially attempted with fewer duplicates (5-10), but 15 requests ensured reliable exploitation
- The success of the attack depends on network latency and server processing speed - local testing environments are more susceptible than production systems with proper load balancing
- Could have automated this with Python's `requests` library and threading, but Burp Suite provides better control and visualization

**Why this works:**
1. User 1 checks stock: 10 available ✓
2. User 2 checks stock: 10 available ✓
3. User 3 checks stock: 10 available ✓
4. All users pass validation simultaneously
5. User 1 processes: 10 - 1 = 9
6. User 2 processes: 10 - 1 = 9 (using stale data)
7. User 3 processes: 10 - 1 = 9 (using stale data)
8. Final stock: -5 (15 orders processed on 10 items)

**Real-world implications:**
- E-commerce overselling during flash sales
- Banking: Multiple withdrawals exceeding account balance
- Ticket booking: Same seat sold to multiple customers
- Coupon/promo code abuse with limited redemptions

**Alternative exploitation methods considered:**
- Python script with `threading` module for concurrent requests
- `curl` commands with GNU Parallel
- Postman collection runner with no delay between requests
- Custom JavaScript in browser console using `fetch()` and `Promise.all()`

