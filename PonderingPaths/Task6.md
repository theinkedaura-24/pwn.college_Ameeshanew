# implict relative paths from /
This challenge asks us to  run /challenge/run using a relative path while having a current working directory of /.

## My solve
**Flag:** `pwn.college{ALKXd3x6SDZG1O5bd6OLoLyP2_1.QX5QTN0wiN4AzNzEzW}`

```hacker@paths~implicit-relative-paths-from-:~$ cd /
hacker@paths~implicit-relative-paths-from-:/$ challenge/run
Correct!!!
challenge/run is a relative path, invoked from the right directory!
Here is your flag:
pwn.college{ALKXd3x6SDZG1O5bd6OLoLyP2_1.QX5QTN0wiN4AzNzEzW}
```

## What I learned
I learned the practical difference between absolute and relative paths in the command line

I also leant that I can change my current working directory using the cd command and that a relative path is determined from my current location.


## References 
I used the resources available on pwn.college as my reference.
