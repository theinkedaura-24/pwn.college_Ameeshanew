# Multiple globs
This challenge asks us to use two * wildcards in a single, short (3 characters or less) pattern to match all files containing the letter 'p'.

## My solve
**Flag:** `pwn.college{ovivZ89JTpl7bflkof8GwBx3s6Z.0lM3kjNxwiN4AzNzEzW}`

```hacker@globbing~multiple-globs:~$ cd /challenge/files
hacker@globbing~multiple-globs:/challenge/files$ /challenge/run *p*
You got it! Here is your flag!
pwn.college{ovivZ89JTpl7bflkof8GwBx3s6Z.0lM3kjNxwiN4AzNzEzW}

```

## What I learned
I learned that we can use multiple globs within a single word to create more complex patterns, like *p*, which finds any file with a 'p' anywhere in its name.

## References 
I used the resources available on pwn.college as my references.
