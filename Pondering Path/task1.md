# The PATH Variable
My task is to stop the /challenge/run program from deleting the flag. The program relies on the rm command, so I need to modify the PATH environment variable to prevent it from being found. 
Because the program is a child process, I must make sure my change to PATH is exported, causing the rm command to fail and leaving the flag for me to read.

## My solve
**Flag:** `pwn.college{AmaOGyePL0mvfXTnxqcdVSVNlf8.QX2cDM1wiN4AzNzEzW}`

```
hacker@path~the-path-variable:~$ export PATH=""
hacker@path~the-path-variable:~$ /challenge/run
Trying to remove /flag...
/challenge/run: line 4: rm: No such file or directory
The flag is still there! I might as well give it to you!
pwn.college{AmaOGyePL0mvfXTnxqcdVSVNlf8.QX2cDM1wiN4AzNzEzW}
```

## What I learned
I learned that the PATH environment variable tells the shell where to look for executable programs. 
By changing or clearing this variable, I can prevent the shell and any programs it runs from finding commands like rm. 
I also learned that for these changes to affect a child process (like a script I run), the variable must be exported.

## References 
I used the resources available on pwn.college as my references.
