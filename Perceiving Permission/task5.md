# Challenge Name
My goal is to get the flag by running the /challenge/run program, but I can't because it's missing the execute permission for my user. 
I need to use the chmod command to add the execute permission (+x) to the /challenge/run file. Once I've made it executable, I can run it to get the flag.

## My solve
**Flag:** `pwn.college{gB-nsSQvX-6sl5VJO4LgD-guZ6S.QXyEjN0wiN4AzNzEzW}`

```hacker@permissions~executable-files:~$ chmod +x /challenge/run
hacker@permissions~executable-files:~$ /challenge/run
Successful execution! Here is your flag:
pwn.college{gB-nsSQvX-6sl5VJO4LgD-guZ6S.QXyEjN0wiN4AzNzEzW}


```

## What I learned
 learned that a file requires the execute (x) permission to be run as a program.
 If this permission is not set for my user (based on user, group, or other rights), the system will refuse to run it and give a "Permission denied" error. 
 I can add this permission using chmod with flags like u+x or a+x.

## References 
I used the resources, videos available on pwn.college as my references.
