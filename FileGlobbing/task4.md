# Matching paths with []
This challenge asks us to use a bracket glob [] as part of a full absolute path to match the same four files, but this time without first changing our directory

## My solve
**Flag:** `pwn.college{o63Nv5Awn-cs4DYp0-0tMdXg9t8.QX0IDO0wiN4AzNzEzW}}`

```
hacker@globbing~matching-paths-with-:~$/challenge/run /challenge/files/file_[bash]
You got it! Here is your flag!
pwn.college{o63Nv5Awn-cs4DYp0-0tMdXg9t8.QX0IDO0wiN4AzNzEzW}

```

## What I learned
I learned that globbing can be applied to any part of a path, not just filenames in the current directory, allowing you to create powerful patterns to reference files from anywhere.

## References 
I used the resources available on pwn.college as my references.
