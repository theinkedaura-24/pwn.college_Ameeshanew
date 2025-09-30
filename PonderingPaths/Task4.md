# Position elsewhere
This challenge will requireus to execute the /challenge/run program from a specific path (which it will tell us). 
We'll need to cd to that directory before rerunning the challenge program. 

## My solve
**Flag:** `pwn.college{YnaQyb2Uhw-TyD6rhdB-30Wv7tg.QX3QTN0wiN4AzNzEzW}`


```hacker@paths~position-elsewhere:~$ /challenge/run
Incorrect...
You are not currently in the /var/lib/apt/lists directory.
Please use the `cd` utility to change directory appropriately.
hacker@paths~position-elsewhere:~$ cd /var/lib/apt/lists
hacker@paths~position-elsewhere:/var/lib/apt/lists$ /challenge/run
Correct!!!
/challenge/run is an absolute path, invoked from the right directory!
Here is your flag:
pwn.college{YnaQyb2Uhw-TyD6rhdB-30Wv7tg.QX3QTN0wiN4AzNzEzW}
```

## What I learned
I learned how to identify the directory of a program by running it and then to cd in the required directory for running the program.

## References 
I used the resources available on pwn.college as my reference.
