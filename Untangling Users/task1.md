# Becoming root with su
Our task is to become the root user using the classic su command. 
When prompted for a password, enter hack-the-planet. After successfully gaining root privileges, find and read the flag to solve the challenge.

## My solve
**Flag:** `pwn.college{sPwPMINB7SdS9q0m-HhGu3ydK7C.QX1UDN1wiN4AzNzEzW}`

```	hacker@users~becoming-root-with-su:~$ su
Password: 
root@users~becoming-root-with-su:/home/hacker# cat flag
cat: flag: No such file or directory
root@users~becoming-root-with-su:/home/hacker# ls
COLLEGE  Desktop  PWN  a  college  f  grep  instructions  myflag  not-the-flag  pwn  the-flag

root@users~becoming-root-with-su:/home/hacker# ^C
root@users~becoming-root-with-su:/home/hacker# ^C
root@users~becoming-root-with-su:/home/hacker# cat not-the-flag
pwn.college{sPwPMINB7SdS9q0m-HhGu3ydK7C.QX1UDN1wiN4AzNzEzW}
```

## What I learned
I learned that su is a setuid binary that lets you substitute users, most commonly to become root. 
It works by running with its owner's (root) permissions and authenticates you by asking for the password of the account I am switching to.

## References 
I used the resources available on pwn.college as my references.
