# Exclusionary globbing
This challenge asks us to use ([!...]) to create a pattern that matches all files except those starting with 'p', 'w', or 'n'.

## My solve
**Flag:** `pwn.college{wuK-3D8oRx8lNujpsEuxuPYSZp8.QX2IDO0wiN4AzNzEzW}`

```hacker@globbing~tab-completion:~$ cat /challenge/pwncollege 
pwn.college{gZEFToBU5WZc-aBFjOcH-Z24tlo.0FN0EzNxwiN4AzNzEzW}
```

## What I learned
We learned that starting a bracket glob with ! or ^ inverts its meaning, causing it to match any single character that is not in the specified set.


## References 
I used the resources available on pwn.college as my references.
