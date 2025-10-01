# Reading files
This challenge asks us to combine the read command with input redirection (<) to read the contents of a file directly into the PWN variable.

## My solve
**Flag:** `pwn.college{01LJgfoccLPyE4s7-jBvzMCh1Lh.QXwIDO0wiN4AzNzEzW}`

```hacker@variables~reading-files:~$ read PWN < /challenge/read_me
You've set the PWN variable properly! As promised, here is the flag:
pwn.college{01LJgfoccLPyE4s7-jBvzMCh1Lh.QXwIDO0wiN4AzNzEzW}

```

## What I learned
I learned an efficient way to read a file's content into a variable by redirecting the file into the read command, avoiding a "Useless Use of Cat".

## References 
I used the resources available on pwn.college as my reference.
