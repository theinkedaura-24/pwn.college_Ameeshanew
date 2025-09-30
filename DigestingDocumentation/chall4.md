# Searching Manuals
This challenge asks us to find the option that will give us the flag by reading the challenge man page.

## My solve
**Flag:** `pwn.college{kd6cE5Qdaz6T6Vi-Gdggx_LVOg3.QX1EDO0wiN4AzNzEzW}`

```hacker@man~reading-manuals:~$ man challenge
hacker@man~reading-manuals:~$ /challenge/challenge--ykjx This argument will give you the flag!
pwn.college{kd6cE5Qdaz6T6Vi-Gdggx_LVOg3.QX1EDO0wiN4AzNzEzW}
```

## What I learned
I learned how to scroll man pages with the arrow keys (and PgUp/PgDn) and search with /.
After searching, we can hit n to go to the next result and N to go to the previous result. Instead of /, you can use ? to search backwards!


## References 
I used the resources available on pwn.college as my reference.
