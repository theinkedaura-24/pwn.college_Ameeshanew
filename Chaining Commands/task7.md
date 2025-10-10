# Understanding Shebangs
My task is to create a standalone executable script at /home/hacker/solve.sh. I must ensure the very first line of the file is a shebang pointing to the bash interpreter (#!/bin/bash). 
The script should then output "hack the planet". Finally, I'll make it executable and run /challenge/run to verify my solution.

## My solve
**Flag:** `pwn.college{YSfHgvdP0IVNI9iWIZJECsGHQP8.0VOzMDOxwiN4AzNzEzW}`

```
hacker@chaining~understanding-shebangs:~$ echo -e '#!/bin/bash\necho "hack the planet"' > /home/hacker/solve.sh
hacker@chaining~understanding-shebangs:~$ chmod +x /home/hacker/solve.sh
hacker@chaining~understanding-shebangs:~$ /challenge/run
Testing your script...
Perfect! Your flag:
Flag: pwn.college{YSfHgvdP0IVNI9iWIZJECsGHQP8.0VOzMDOxwiN4AzNzEzW}
```

## What I learned
I learned that the shebang (#!) is a special directive that must be the absolute first line in a script. It tells the operating system which interpreter (like /bin/bash or /usr/bin/python3) should be used to run the file. 
This makes a script truly self-contained and directly executable by the system, not just from within another shell.

## References 
I used the resources available on pwn.college as my references.
