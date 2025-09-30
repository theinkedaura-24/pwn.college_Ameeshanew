# Matching with *
This challenge asks us to navigate to the /challenge directory using a path with a wildcard that is four characters or shorter.

## My solve
**Flag:** `pwn.college{8Hvnb_VL6dthrJUzardMX1Wyx0n.QXxIDO0wiN4AzNzEzW}`

```hacker@globbing~matching-with-:~$ cd /ch*
hacker@globbing~matching-with-:/challenge$ /challenge/run
You ran me with the working directory of /challenge! Here is your flag:
pwn.college{8Hvnb_VL6dthrJUzardMX1Wyx0n.QXxIDO0wiN4AzNzEzW}

```

## What I learned
I learned that the asterisk (*) glob is a wildcard that matches any sequence of characters, allowing us to shorten commands by using patterns instead of full directory or filenames.

## References 
I used the resources available on pwn.college as my reference.
