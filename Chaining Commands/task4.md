# Your First Shell Script
My task is to run /challenge/pwn and then /challenge/college using a shell script. 
I need to create a file named x.sh and write the two commands on separate lines inside it. To get the flag, I will then execute my script by running the command bash x.sh.

## My solve
**Flag:** `pwn.college{ciwvDbFYzDib0uWarz3kk_UTl69.QXxcDO0wiN4AzNzEzW}`

```	hacker@chaining~your-first-shell-script:~$ echo /challenge/pwn /challenge/college > x
hacker@chaining~your-first-shell-script:~$ bash x.sh
Great job, you've written your first shell script! Here is the flag:
pwn.college{ciwvDbFYzDib0uWarz3kk_UTl69.QXxcDO0wiN4AzNzEzW}

```

## What I learned
I learned that I can automate a sequence of commands by creating a shell script, which is a text file containing the commands. Instead of typing them manually, I can save them in a file (like script.sh). 
I can then execute all the commands in the file by running it with a shell, for example, bash script.sh.

## References 
I used the resources available on pwn.college as my refereces.
