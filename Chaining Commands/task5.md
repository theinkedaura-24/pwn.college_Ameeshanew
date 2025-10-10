# Redirecting Script Output
My task is to pipe the combined output from two programs into a final solver program. First, I'll create a shell script that contains the /challenge/pwn and /challenge/college commands. 
Then, I'll execute that script and use the pipe operator (|) to redirect its entire output into /challenge/solve to get the flag.

## My solve
**Flag:** `pwn.college{kC17P-efA0ptXxuKeZ3XH0a4Upj.QX4ETO0wiN4AzNzEzW}`


```hacker@chaining~redirecting-script-output:~$ echo -e "/challenge/pwn\n/challenge/college" > myscript.sh
hacker@chaining~redirecting-script-output:~$ bash myscript.sh | /challenge/solve
Correct! Here is your flag:
pwn.college{kC17P-efA0ptXxuKeZ3XH0a4Upj.QX4ETO0wiN4AzNzEzW}
```

## What I learned
I learned that a shell script can be treated like a single command, which means I can redirect its input and output. Specifically, I can use the pipe operator (|) to send the combined standard output from all the commands inside a script to the standard input of another program. 
This is a great way to group the output of multiple commands for processing.
 
## References 
I used the resources available on pwn.college as my references.
