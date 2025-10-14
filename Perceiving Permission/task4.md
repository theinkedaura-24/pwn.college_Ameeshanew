# Changing Permissions
My task is to read the /flag file, but its permissions (-r--------) only allow the root user to read it. 
For this level, I've been given special power to use the chmod command on any file. 
I need to use this command to add read permission to /flag for my user, which will then allow me to view its contents and capture the flag.

## My solve
**Flag:** `pwn.college{sT60zx8_UZsdU9FK3nn0ADjju5I.QXzcjM1wiN4AzNzEzW}`

```
hacker@permissions~changing-permissions:~$chmod o+r /flag
cat /flag
pwn.college{sT60zx8_UZsdU9FK3nn0ADjju5I.QXzcjM1wiN4AzNzEzW}

```

## What I learned
I learned that file permissions in Linux are defined for the user (owner), group, and other in sets of r (read), w (write), and x (execute). The chmod command is used to change these permissions.
I can use its symbolic mode to add (+) or remove (-) specific rights for different users (like u, g, o) to precisely control file access.

## References 
I used the resources available on pwn.college as my references.
