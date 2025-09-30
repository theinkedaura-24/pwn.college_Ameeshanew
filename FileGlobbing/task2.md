# Matching with ?
This challenge asks us to go to the /challenge directory by using the ? wildcard to replace the letters 'c' and 'l' in the path.


## My solve
**Flag:** `pwn.college{sClh_17iP4nvVLAfsUzPV4MxO-r.QXyIDO0wiN4AzNzEzW}`

```
hacker@globbing~matching-with-:~$ cd /?ha??enge
hacker@globbing~matching-with-:/challenge$ /challenge/run
You ran me with the working directory of /challenge! Here is your flag:
pwn.college{sClh_17iP4nvVLAfsUzPV4MxO-r.QXyIDO0wiN4AzNzEzW}

```

## What I learned
I learned that the question mark (?) is a wildcard that matches exactly one character, which allows for more specific and controlled pattern matching than the asterisk (*).


## References 
I used the resources available on pwn.college as my references.
