# Groups and Files
My goal is to read /flag, which is readable by its owning group, currently root. 
I need to use the chgrp command, which I have special permission to run for this challenge, to change the file's group ownership to hacker. 
Since I am a member of the hacker group, this will give me the permission required to read the flag.

## My solve
**Flag:** `pwn.college{0MrIiLiE-CyrA9vaEzO_tXlyf6u.QXxcjM1wiN4AzNzEzW}`
```
hacker@permissions~groups-and-files:~$ cat /flag
pwn.college{0MrIiLiE-CyrA9vaEzO_tXlyf6u.QXxcjM1wiN4AzNzEzW}

```

## What I learned
I learned that in addition to a user owner, every file has a group owner that can be used to grant access to multiple users. 
The chgrp (change group) command allows me to change this group ownership. By changing a file's group to one I'm a member of, I can gain access based on group permissions.

## References 
I used the videos on pwn.college as my references.
