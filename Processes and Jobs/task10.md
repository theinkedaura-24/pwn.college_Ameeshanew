# Processing exit codes
This challenge asks us to get the exit code from one program and use it as an input for another program.

To solve this, we need to:

Run the /challenge/get-code program.

Immediately after it finishes, check its exit code by typing echo $?.

Take the number that's printed, and run /challenge/submit-code with that number as an argument to get the flag.

## My solve
**Flag:** `pwn.college{Qh-c0lPBqKfVnFlk1MCeJshDhBY.QX5YDO1wiN4AzNzEzW}`

```hacker@processes~process-exit-codes:~$ /challenge/get-code
Exiting with an error code!
hacker@processes~process-exit-codes:~$ /challenge/submit-code $?
CORRECT! Here is your flag:
pwn.college{Qh-c0lPBqKfVnFlk1MCeJshDhBY.QX5YDO1wiN4AzNzEzW}

```

## What I learned
I learned that every command in Linux, whether it succeeds or fails, returns an exit code when it finishes. This code is a number that tells the shell if the command ran correctly. A code of 0 usually means success, while a non-zero value (like 1) indicates some kind of failure or problem.

I also learned how to access this exit code using the special shell variable $?. By echoing this variable right after a command runs, I can see its exit code.

The challenge required me to use this knowledge in a two-step process:

First, I ran the /challenge/get-code program.

Then, I immediately checked its exit code using echo $?.

Finally, I took that number and passed it as an argument to the /challenge/submit-code program to get the flag.

This taught me a fundamental concept in shell scripting and automation: using exit codes to check the success of a command and make decisions based on the outcome.

## References 
I used the resources available on pwn.college as my references.
