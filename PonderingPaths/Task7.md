# Challenge Name
Previously, our relative path was "naked": it directly specified the directory to descend into from the current directory.
In this level, we explored more explicit relative paths.

## My solve
**Flag:** `pwn.college{w6gbp3s4jUTNixvwXmrzAVfbTvd.QXwUTN0wiN4AzNzEzW}`


```hacker@paths~explicit-relative-paths-from-:~$ cd /
hacker@paths~explicit-relative-paths-from-:/$ ./challenge/run
Correct!!!
./challenge/run is a relative path, invoked from the right directory!
Here is your flag:
pwn.college{w6gbp3s4jUTNixvwXmrzAVfbTvd.QXwUTN0wiN4AzNzEzW}
```

## What I learned
I learned that ./challenge/run is a relative path and I learnt how to correctly invoke it by cding into the right directory.

## References 
I used the resources available on pwn.college as my reference.
