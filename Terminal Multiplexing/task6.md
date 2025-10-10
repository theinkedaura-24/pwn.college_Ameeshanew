# Switching Windows (tmux)
My task is to find a flag by switching windows inside a tmux session. After I attach to the session, I'll start in Window 1, but the flag is in Window 0. 
I need to use a keyboard shortcut like Ctrl-B, p (previous window) or Ctrl-B, 0 to navigate to the correct window and find the flag.

## My solve
**Flag:** `pwn.college{olD55q2-Z07YSWkQG1KD0-Wxfg5.0FM5IDOxwiN4AzNzEzW

```hacker@terminal-multiplexing~switching-windows-tmux:~$  cat <<MSG
> Excellent work! You found window 0!
> Here is your flag: pwn.college{olD55q2-Z07YSWkQG1KD0-Wxfg5.0FM5IDOxwiN4AzNzEzW}
```

## What I learned
I learned how to manage multiple windows within a single tmux session, which is great for organization. The keyboard shortcuts are all prefixed with Ctrl-B, such as c to create a window, n and p to navigate between them, and 0-9 to jump directly to a window. 
I also learned that tmux displays a helpful status bar showing all my windows at the bottom of the screen.

## References 
I used the resources and documents available on pwn.college as my references.
