# Redirecting output
This challenge asks you to use the output redirection operator (>) to save the word "PWN" into a file named "COLLEGE".

## My solve
**Flag:** `pwn.college{wyKYj40zupulLyBNfO3viVjToaS.QX0YTN0wiN4AzNzEzW}`

```
hacker@piping~redirecting-output:~$ echo PWN > COLLEGE
Correct! You successfully redirected 'PWN' to the file 'COLLEGE'! Here is your 
flag:
pwn.college{wyKYj40zupulLyBNfO3viVjToaS.QX0YTN0wiN4AzNzEzW}

```

## What I learned
I learned that the > symbol redirects a command's standard output (stdout), allowing us to capture what it would normally print to the screen and save it directly into a file.

## References 
I used the resources available on pwn.college as my references.
