# The Root
The challenge asks us to invoke a program called pwn in /.

## My solve
**Flag:** `pwn.college{8fZsbvR7cvtAGrbgpZ1W_P0Ku7z.QX4cTO0wiN4AzNzEzW}`

The challenge asked me to invoke a program called pwn in /, so I input /pwn and got my flag. 

## What I learned
I learned that the filesystem starts at /. Under that, there is a whole mess of other directories, configuration files, programs, and, most importantly, flags.You can invoke a program by providing its path on the command line. Here the path is /pwn.
This style of path, one that starts with the root directory, is referred to as an "absolute path".

## References 
I used the video available on pwn.college as my reference.


