# Matching with []
This challenge asks you to use a single bracket glob [] to create one pattern that expands into four specific filenames: file_b, file_a, file_s, and file_h.

## My solve
**Flag:** `pwn.college{IfuvypN899SZkEEuu14R2gcdezP.QXzIDO0wiN4AzNzEzW}`

```
hacker@globbing~matching-with-:~$ cd /challenge/files
hacker@globbing~matching-with-:/challenge/files$ /challenge/run file_[bash]
You got it! Here is your flag!
pwn.college{IfuvypN899SZkEEuu14R2gcdezP.QXzIDO0wiN4AzNzEzW}

```

## What I learned
I learned that bracket globs [] match any one character from the specific set listed inside the brackets, letting us create custom, single-character wildcards.

## References 
I used the resources available on pwn.college for my references.
