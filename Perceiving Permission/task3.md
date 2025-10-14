# Fun with group names
My goal is to change the group of /flag, but this time my user's group name is randomized and isn't hacker. 
First, I need to run the id command to find out the actual name of the group I belong to. 
Then, I will use that group name with the chgrp command on /flag to get the permissions I need to read it.

## My solve
**Flag:** `pwn.college{4AqJKqGqlI14r7_NaHdXBwTwl3A.QXycjM1wiN4AzNzEzW}`

```
hacker@permissions~fun-with-groups-names:~$ id
uid=1000(hacker) gid=1000(grp29441) groups=1000(grp29441)
hacker@permissions~fun-with-groups-names:~$ chgrp grp29441 /flag
hacker@permissions~fun-with-groups-names:~$ cat /flag
pwn.college{4AqJKqGqlI14r7_NaHdXBwTwl3A.QXycjM1wiN4AzNzEzW}

```

## What I learned
I learned that a user's group doesn't always have the same name as the username. 
The definitive way to find my user ID, group ID, and all group memberships is by using the id command.
I must use this command to find the correct group name before using it in commands like chgrp.

## References 
I used the videos available on pwn.college as my references.
