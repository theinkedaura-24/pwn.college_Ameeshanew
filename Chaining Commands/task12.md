# Reading Shell Scripts
My task is to find a password that has been hardcoded inside the /challenge/run shell script. 
I need to use a command like cat to read the script's source code and find the secret. Once I've discovered the password, I'll run the program and enter it to get the flag.

## My solve
**Flag:** `pwn.college{4RJwGCw8GsID2qyv0iRkDCy8gDl.0lMwgDOxwiN4AzNzEzW}}`

```hacker@chaining~reading-shell-scripts:~$ cat /challenge/run
#!/opt/pwn.college/bash

read GUESS
if [ "$GUESS" == "hack the PLANET" ]
then
        echo "CORRECT! Your flag:"
        cat /flag
else
        echo "Read the /challenge/run file to figure out the correct password!"
fi
hacker@chaining~reading-shell-scripts:~$ /challenge/run
hack the PLANET
CORRECT! Your flag:
pwn.college{4RJwGCw8GsID2qyv0iRkDCy8gDl.0lMwgDOxwiN4AzNzEzW}
```

## What I learned
I learned that reading the source code of shell scripts is a critical skill for understanding how they work. By using a command like cat, I can inspect a script's logic and often find important information, such as hardcoded passwords, written directly into the file. 
This is a valuable technique for both debugging and security analysis.

## References 
I used the resources available on pwn.college as my references.
