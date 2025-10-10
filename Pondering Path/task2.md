# Setting Path
My task is to enable the /challenge/run program to find and execute the win command. The win command is located in /challenge/more_commands/, a directory not in the default PATH. 
I need to solve this by setting my PATH variable to that directory and exporting it, allowing /challenge/run to successfully locate and run win.

## My solve
**Flag:** `pwn.college{helloworld}`

type in your solve and your thought process behind solving the challenge. Include as much as info as possible.. Use triple ticks for bash.
```hacker@path~setting-path:~$ export PATH="/challenge/more_commands/"
hacker@path~setting-path:~$ /challenge/run
Invoking 'win'....
Congratulations! You properly set the flag and 'win' has launched!
pwn.college{kfb_zuo9XXoWX40hdg0tmFbL0oZ.QX1cjM1wiN4AzNzEzW}
```

## What I learned
I learned that I can modify the PATH variable to include custom directories, making the executables within them runnable by name from anywhere.
By setting the PATH, I can add my own scripts and programs to the shell's search path, which is a powerful way to customize my environment and improve my workflow.

## References 
I used the resources available on pwn.college as my references.
