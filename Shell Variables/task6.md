# Storing command output
This challenge asks us to use command substitution ($(...)) to capture the output of the /challenge/run command and store it in the PWN variable.

## My solve
**Flag:** `pwn.college{0K9w9rWT67q_0j4I0QK9zxOoIHn.QX1cDN1wiN4AzNzEzW}`

```	hacker@variables~storing-command-output:~$ PWN=$(/challenge/run)
Congratulations! You have read the flag into the PWN variable. Now print it out 
and submit it!
hacker@variables~storing-command-output:~$ echo "$PWN"
pwn.college{0K9w9rWT67q_0j4I0QK9zxOoIHn.QX1cDN1wiN4AzNzEzW}

```

## What I learned
I learned that the $(...) syntax executes a command and substitutes its output as a value, which is the standard way to save the result of a command into a variable.

## References 
I used the resources available on pwn.college as my references.
