# Detaching and Attaching (tmux)
My task is to perform a detach and reattach sequence using tmux. I will start a new session by running tmux, then detach from it by pressing Ctrl-B followed by d. 
From my original shell, I'll run /challenge/run, and then I'll use tmux attach to re-enter the session and find the flag.

## My solve
**Flag:** `pwn.college{wHq5GZYHZILjAKMZHr3oxvBwASS.0VO4IDOxwiN4AzNzEzW}

```hacker@terminal-multiplexing~detaching-and-attaching-tmux:~$  echo Congratulations, here is your flag: pwn.college{wHq5GZYHZILjAKMZHr3oxvBwASS.0VO4IDOxwiN4AzNzEzW}
Congratulations, here is your flag: pwn.college{wHq5GZYHZILjAKMZHr3oxvBwASS.0VO4IDOxwiN4AzNzEzW}
```

## What I learned
I learned the basics of tmux, a modern terminal multiplexer similar to screen. The key difference is that its command prefix is Ctrl-B instead of Ctrl-A. 
I now know the commands to launch it (tmux), detach from it (Ctrl-B, d), list sessions (tmux ls), and reattach (tmux attach).

## References 
I used the documents and resources available on pwn.college as my references.
