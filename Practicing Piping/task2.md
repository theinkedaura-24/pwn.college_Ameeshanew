# Redirecting more output
type what the challenge asks

## My solve
**Flag:** `pwn.college{kcWJJu8mc_3dLykHEQ-ezPFEoPm.QX1YTN0wiN4AzNzEzW}`

```
hacker@piping~redirecting-more-output:~$ /challenge/run > myflag
[INFO] WELCOME! This challenge makes the following asks of you:
[INFO] - the challenge will check that output is redirected to a specific file path : myflag
[INFO] - the challenge will output a reward file if all the tests pass : /flag

[HYPE] ONWARDS TO GREATNESS!

[INFO] This challenge will perform a bunch of checks.
[INFO] If you pass these checks, you will receive the /flag file.

[TEST] You should have redirected my stdout to a file called myflag. Checking...

[PASS] The file at the other end of my stdout looks okay!
[PASS] Success! You have satisfied all execution requirements.
hacker@piping~redirecting-more-output:~$ cat myflag

[FLAG] Here is your flag:
[FLAG] pwn.college{kcWJJu8mc_3dLykHEQ-ezPFEoPm.QX1YTN0wiN4AzNzEzW}

```

## What I learned
I learned that output redirection works for any command, not just echo, and we're introduced to the concept that programs have two output streams: standard output (for data) and standard error (for messages).

## References 
I used the resources available on pwn.college as my references.
