# Position yet elsewhere
This challenge will require us  to execute the /challenge/run program from a specific path (which it will tell us). 
We'll need to cd to that directory before rerunning the challenge program. 

## My solve
**Flag:** `pwn.college{wImuQP0rs7mMUqc9LaeOQkkQEA2.QX4QTN0wiN4AzNzEzW}`

```hacker@paths~position-yet-elsewhere:~$ /challenge/run
Incorrect...
You are not currently in the /proc/138 directory.
Please use the `cd` utility to change directory appropriately.
hacker@paths~position-yet-elsewhere:~$ cd /proc/138
hacker@paths~position-yet-elsewhere:/proc/138$ /challenge/run
Correct!!!
/challenge/run is an absolute path, invoked from the right directory!
Here is your flag:
pwn.college{wImuQP0rs7mMUqc9LaeOQkkQEA2.QX4QTN0wiN4AzNzEzW}
```

## What I learned
I learned how to execute the /challenge/run program from a specific path, by switching directories using cd.

## References 
I used the resources available on pwn.college as my reference.
