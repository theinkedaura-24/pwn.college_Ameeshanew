# more catting practice
Retrieving the flag by using its absolute path but no cd is allowed.


## My solve
**Flag:** `pwn.college{ImACUXg1-N_HD8r4Hg3g1Y5lagK.QXwITO0wiN4AzNzEzW}`
We are told to find the flag in this directory( /usr/include/ncursesw/flag) using cat but the challenge forbids changing directories (cd), so the only way to read the flag is to cat the file by its absolute path
```
hacker@commands~more-catting-practice:~$ cat /usr/include/ncursesw/flag
pwn.college{ImACUXg1-N_HD8r4Hg3g1Y5lagK.QXwITO0wiN4AzNzEzW}

```
## What I learned
When we are not allowed to change directories, absolute paths are used. Thus, we can read any file if we know or we discover its full path.

## References 
I used the videos and documents available on pwn.college as my references.
