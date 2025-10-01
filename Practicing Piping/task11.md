# Process substitution for input
This challenge asks us to use process substitution (<(...)) to directly compare the live outputs of two programs using the diff command, finding the one line (the flag) that is different.

## My solve
**Flag:** `pwn.college{sDXWnYKquf5ct9PfMZIytiYhrip.0lNwMDOxwiN4AzNzEzW}`

```hacker@piping~process-substitution-for-input:~$ diff <(/challenge/print_decoys) <(/challenge/print_decoys_and_flag)
83a84
> pwn.college{sDXWnYKquf5ct9PfMZIytiYhrip.0lNwMDOxwiN4AzNzEzW}

```

## What I learned
I learned that process substitution lets us treat a command's output as if it were a temporary file, allowing us to feed it directly as a filename argument to another command like diff.

## References 
I used the resources available on pwn.college as my reference.
