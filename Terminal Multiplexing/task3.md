# Finding Sessions
My task is to find a flag hidden in one of three running screen sessions. First, I need to use the screen -ls command to list all the sessions. Then, I'll reattach to each one individually using screen -r <session_name>, remembering to detach with Ctrl-A, d after checking each one, until I find the correct session with the flag.

## My solve
**Flag:** `pwn.college{Af6AjSb7iIcrH-WnuB1ey0hZDq-.01N4IDOxwiN4AzNzEzW}`

```hacker@terminal-multiplexing~finding-sessions:~$  echo 'Congratulations! You found the right session!'
Congratulations! You found the right session!
hacker@terminal-multiplexing~finding-sessions:~$  echo pwn.college{Af6AjSb7iIcrH-WnuB1ey0hZDq-.01N4IDOxwiN4AzNzEzW}
pwn.college{Af6AjSb7iIcrH-WnuB1ey0hZDq-.01N4IDOxwiN4AzNzEzW}
```

## What I learned
 I learned how to manage multiple screen sessions simultaneously. The command screen -ls lists all my active sessions, and I can connect to a specific one by its name or PID using screen -r <session_name>.
 This is a crucial skill for organizing my work when I have several background tasks running.

## References 
I used the resources available on pwn.college as my references.
