# Redirecting input
In this challenge, we first create a file named PWN with the content COLLEGE using output redirection (>). 
Then we must use the input redirection operator (<) to feed that file into the /challenge/run program.

## My solve
**Flag:** `pwn.college{kxccckHyioZcakKL_eZXTYL7_J-.QXwcTN0wiN4AzNzEzW}`

```hacker@piping~redirecting-input:~$ echo COLLEGE > PWN
hacker@piping~redirecting-input:~$ cat PWN
COLLEGE
hacker@piping~redirecting-input:~$ /challenge/run < PWN
Reading from standard input...
Correct! You have redirected the PWN file into my standard input, and I read 
the value 'COLLEGE' out of it!
Here is your flag:
pwn.college{kxccckHyioZcakKL_eZXTYL7_J-.QXwcTN0wiN4AzNzEzW}

```

## What I learned
I learned how to use the < operator to redirect a file's contents to a program's standard input (stdin).

## References 
I used the resources available on pwn.college as my references.
