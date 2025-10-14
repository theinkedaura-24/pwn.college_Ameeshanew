# Help for Bullitins
This challenge's challenge command is a shell builtin, rather than a program. We need to lookup its help to figure out the secret value to pass to it!

## My solve
**Flag:** `pwn.college{os1xmmVze-h6BQJSgrJVwEL0arB.QX0ETO0wiN4AzNzEzW}`


```
hacker@man~help-for-builtins:~$ help challenge
challenge: challenge [--fortune] [--version] [--secret SECRET]
    This builtin command will read you the flag, given the right arguments!
    
    Options:
      --fortune         display a fortune
      --version         display the version
      --secret VALUE    prints the flag, if VALUE is correct

    You must be sure to provide the right value to --secret. That value
    is "os1xmmVz".
hacker@man~help-for-builtins:~$ challenge --secret os1xmmVz
Correct! Here is your flag!
pwn.college{os1xmmVze-h6BQJSgrJVwEL0arB.QX0ETO0wiN4AzNzEzW}

```

## What I learned
I learned how to use help to look up help for builtins

## References 
I used the resources available on pwn.college as my references.
