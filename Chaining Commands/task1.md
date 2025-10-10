# Chaining with semicolons
My task is to run two separate programs, /challenge/pwn and then /challenge/college, in a specific sequence. 
To do this, I must execute them on a single command line by using the semicolon (;) to chain them together.

## My solve
**Flag:** `pwn.college{ET1jKqt6L9G8MG-FUN-2zwyz-6g.QX1UDO0wiN4AzNzEzW}`

```hacker@chaining~chaining-with-semicolons:~$ /challenge/pwn ; /challenge/college
Yes! You chained /challenge/pwn and /challenge/college! Here is your flag:
pwn.college{ET1jKqt6L9G8MG-FUN-2zwyz-6g.QX1UDO0wiN4AzNzEzW}

```

## What I learned
I learned that the semicolon (;) is a command separator that lets me run multiple commands sequentially on a single line. 
The shell executes the first command, waits for it to finish, and then immediately executes the next one. This is a basic but useful way to chain commands together.

## References 
I used the resources available on pwn.college as my references.
