# Helpful programs
In this challenge we practice reading a program's documentation with --help.

## My solve
**Flag:** `pwn.college{M0WTLI4NWKjDQWvl_1sy9iAXGey.QX3IDO0wiN4AzNzEzW}`


```
hacker@man~helpful-programs:~$ /challenge/challenge --help
usage: a challenge to make you ask for help [-h] [--fortune] [-v] [-g GIVE_THE_FLAG] [-p]

optional arguments:
  -h, --help            show this help message and exit
  --fortune             read your fortune
  -v, --version         get the version number
  -g GIVE_THE_FLAG, --give-the-flag GIVE_THE_FLAG
                        get the flag, if given the correct value
  -p, --print-value     print the value that will cause the -g option to give you the flag
hacker@man~helpful-programs:~$ /challenge/challenge -p
The secret value is: 41
hacker@man~helpful-programs:~$ /challenge/challenge -g 41
Correct usage! Your flag: pwn.college{M0WTLI4NWKjDQWvl_1sy9iAXGey.QX3IDO0wiN4AzNzEzW}


```

## What I learned
I learned how to use the --help command and using that I learnt how to run the program given doing which I got my flag.

## References 
I used the resources available on pwn.college as my reference.
