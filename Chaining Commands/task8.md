# Scripting with Arguments
My task is to create a script at /home/hacker/solve.sh that takes two command-line arguments. Inside the script, I need to use the special variables $1 and $2 to access these arguments. 
The script's job is to print them back out in reverse order (the second argument, then the first). Finally, I'll run /challenge/run to verify my script and get the flag.

## My solve
**Flag:** `pwn.college{c2q8pew7hiFa9gusTb97gan_TVG.0VNzMDOxwiN4AzNzEzW}`

```hacker@chaining~scripting-with-arguments:~$ echo -e '#!/bin/bash\necho "$2 $1"' > /home/hacker/solve.sh
hacker@chaining~scripting-with-arguments:~$ chmod +x /home/hacker/solve.sh
hacker@chaining~scripting-with-arguments:~$ /challenge/run
Correct! Your script properly reversed the arguments.
Here's your flag:
pwn.college{c2q8pew7hiFa9gusTb97gan_TVG.0VNzMDOxwiN4AzNzEzW}
```

## What I learned
I learned how to make my shell scripts more powerful by accepting command-line arguments. I can access these arguments inside the script using special positional variables: $1 for the first argument, $2 for the second, and so on. 
This allows me to write flexible scripts that can operate on different inputs without changing the code itself.

## References 
I used the resources available on pwn.college as my references.
