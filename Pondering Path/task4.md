# Adding commands
My task is to create my own executable script named win that the /challenge/run program can execute. I'll write a script that reads the flag, put it in a directory, and then add that directory to the PATH. 
I have to be careful not to break the cat command inside my script, so I'll either use its full path or append my directory to the existing PATH instead of replacing it

## My solve
**Flag:** `pwn.college{QMtD9d713aMFp6QhRX-vctxwwxR.QX2cjM1wiN4AzNzEzW}`

type in your solve and your thought process behind solving the challenge. Include as much as info as possible.. Use triple ticks for bash.
```hacker@path~adding-commands:~$ which cat
/run/dojo/bin/cat
hacker@path~adding-commands:~$ echo -e '#!/bin/bash\n/run/dojo/bin/cat /flag' > ~/win
hacker@path~adding-commands:~$ /bin/chmod +x ~/win
hacker@path~adding-commands:~$ export PATH="$HOME"
hacker@path~adding-commands:~$ /challenge/run
Invoking 'win'....
pwn.college{QMtD9d713aMFp6QhRX-vctxwwxR.QX2cjM1wiN4AzNzEzW}
```

## What I learned
I  learned how to create a custom command by writing a script and adding its directory to the PATH. More importantly, I learned that overwriting PATH is dangerous because it can break dependencies on system commands. 
The proper way to add a new directory is to append it to the existing path, like export PATH=$PATH:/my/new/dir, to preserve access to essential tools

## References 
I used the resources available on pwn.college as my references.
