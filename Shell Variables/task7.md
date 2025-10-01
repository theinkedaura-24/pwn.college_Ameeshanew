# Reading input
This challenge asks us to use the read command to capture input that you type into the terminal and store the value "COLLEGE" into the PWN variable.

## My solve
**Flag:** `pwn.college{ko7t8_DjRWHgyoS-s1j4-xF1zo0.QX4cTN0wiN4AzNzEzW}`

```hacker@variables~reading-input:~$ read -p "INPUT: " PWN
INPUT: COLLEGE
You've set the PWN variable properly! As promised, here is the flag:
pwn.college{ko7t8_DjRWHgyoS-s1j4-xF1zo0.QX4cTN0wiN4AzNzEzW}

```

## What I learned
I learned that the read command is the standard way to make a shell script interactive by pausing to accept user input and saving it into a variable.

## References 
I used the resources available on pwn.college as my refereces.
