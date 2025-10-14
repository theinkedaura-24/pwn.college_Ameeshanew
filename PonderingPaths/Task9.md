# home sweet home
This challenge asks us to provide an absolute path to a file in our home directory using three characters or less.


## My solve
**Flag:** `pwn.college{8GaDeG-iy1nNel_iWy8bo2V0ngD.QXzMDO0wiN4AzNzEzW}`

```
hacker@paths~home-sweet-home:~$ /challenge/run ~/a
Writing the file to /home/hacker/a!
... and reading it back to you:
pwn.college{8GaDeG-iy1nNel_iWy8bo2V0ngD.QXzMDO0wiN4AzNzEzW}
```

## What I learned
I learnt that the shell expands (~) into the full, absolute path of our home directory before a command is run, which is a powerful shortcut.

## References 
I used the resources available on pwn.college as my reference.
