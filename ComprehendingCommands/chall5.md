# comparing files
It tells us to how to use the diff command.

## My solve
**Flag:** `pwn.college{Aj1SEbtWfNyn48mH5Hl0NnG1O29.01MwMDOxwiN4AzNzEzW}`

In this task I first read the two given files using 'cat' function and then I differentiate them line by line using the 'diff' function.
```

hacker@commands~comparing-files:~$ cat /challenge/decoys_only.txt
hacker@commands~comparing-files:~$ cat /challenge/decoys_and_real.txt
hacker@commands~comparing-files:~$ diff /challenge/decoys_only.txt /challenge/decoys_and_real.txt
2a3
> pwn.college{Aj1SEbtWfNyn48mH5Hl0NnG1O29.01MwMDOxwiN4AzNzEzW}

```

## What I learned
I learned how to use the diff command to differentiate two files line by line which would have otherwise been a very difficult task simply by eyeballing.

## References 
I used the videos and documents available on pw.college as my reference.
