# Redirecting errors
This challenge asks us to redirect a program's standard output (> or 1>) and its standard error (2>) into two different files within the same command.

## My solve
**Flag:** `pwn.college{sdweIK42D4C37rNSZY2EiP4pqa7.QX3YTN0wiN4AzNzEzW}`

```
hacker@piping~redirecting-errors:~$ /challenge/run > myflag 2> instructions
hacker@piping~redirecting-errors:~$ cat myflag

[FLAG] Here is your flag:
[FLAG] pwn.college{sdweIK42D4C37rNSZY2EiP4pqa7.QX3YTN0wiN4AzNzEzW}

```

## What I learned
I learned about File Descriptors (FDs), specifically that standard output is FD 1 and standard error is FD 2, and how to explicitly use these numbers to control where different output streams are saved.

## References 
I used the resources available on pwn.college as my references.
