# Detaching and Attaching
My task is to detach and reattach to a screen session. First, I'll start screen and then detach using the Ctrl-A, d key combination. From my original shell, I'll run /challenge/run to send the flag to my detached session. 
Finally, I'll reattach to the session using screen -r to find the flag waiting inside.

## My solve
**Flag:** `pwn.college{E4GslV6Dekh2CZd1CZVgkvKHRwR.0lN4IDOxwiN4AzNzEzW}`

```hacker@terminal-multiplexing~detaching-and-attaching:~$ /challenge/run
Your screen session is still attached!

You need to detach from your screen session first.
Press Ctrl-A then d to detach.
hacker@terminal-multiplexing~detaching-and-attaching:~$
hacker@terminal-multiplexing~detaching-and-attaching:~$ echo Yes! Flag is: pwn.college{E4GslV6Dekh2CZd1CZVgkvKHRwR.0lN4IDOxwiN4AzNzEzW}
Yes! Flag is: pwn.college{E4GslV6Dekh2CZd1CZVgkvKHRwR.0lN4IDOxwiN4AzNzEzW}
```

## What I learned
I learned the main power of screen: I can detach from a session, leaving it running in the background, and reattach to it later. I detach by pressing Ctrl-A then d. 
To get back into the session, I use the command screen -r. This is perfect for keeping processes running even if my connection drops.

## References 
I used the resources available on pwn.college as my references.
