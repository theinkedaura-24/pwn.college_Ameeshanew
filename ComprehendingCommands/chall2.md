# catting absolute paths
Read the flag file made readable at the absolute path /flag using the cat command.

## My solve
**Flag:** `pwn.college{MYpzZyNsIiKnOjVeWhl7vC4M96B.QX5ETO0wiN4AzNzEzW}`
I followed the challenge instructions which state that the flag file will be made readable at the absolute path /flag. Since cat accepts absolute paths, the simplest way to read the file is to run cat /flag.
```	hacker@commands~catting-absolute-paths:~$ cat /flag
pwn.college{MYpzZyNsIiKnOjVeWhl7vC4M96B.QX5ETO0wiN4AzNzEzW}
```

## What I learned
I learned that cat takes absolute paths and will print file contents regardless of the file's location.

## References 
Videos and documents available on pwn.college
