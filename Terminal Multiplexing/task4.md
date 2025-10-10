# Switching Windows
My task is to find a flag hidden in a different window within a running screen session. 
First, I'll attach to the session using screen -r. Once inside, I will use a keyboard shortcut like Ctrl-A, n (next window) or Ctrl-A, 1 to switch to the second window, where the flag is waiting for me.



## My solve
**Flag:** `pwn.college{80UEs6C0ctjUwXOCM52ZasMKHJ2.0FO4IDOxwiN4AzNzEzW}`


```Excellent work! You found window 0!
Here is your flag: pwn.college{80UEs6C0ctjUwXOCM52ZasMKHJ2.0FO4IDOxwiN4AzNzEzW}
```

## What I learned
I learned that a single screen session can manage multiple windows, which are like tabs in a browser. I can control these windows using keyboard shortcuts that all start with Ctrl-A. 
Key commands include c to create a new window, n and p to navigate between them, and 0-9 to jump directly to a specific window.

## References 
I used the resources available on pwn.college as my references.
