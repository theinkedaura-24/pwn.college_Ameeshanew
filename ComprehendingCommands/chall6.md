# listing files
The challenge asks us to learn to list directory contents using the ls command!

## My solve
**Flag:** `pwn.college{k2xI9-uYYZd7i2JSGwtPkTujq2q.QX4IDO0wiN4AzNzEzW}`

```
hacker@commands~listing-files:~$ ls /challenge
10576-renamed-run-26039  DESCRIPTION.md
hacker@commands~listing-files:~$ /challenge/10576-renamed-run-26039
Yahaha, you found me! Here is your flag:
pwn.college{k2xI9-uYYZd7i2JSGwtPkTujq2q.QX4IDO0wiN4AzNzEzW}
```

## What I learned
I learned that the ls command will list files in all the directories provided to it as arguments, and in the current directory if no arguments are provided.

## References 
I used the resources available on pwn.college as my reference.
