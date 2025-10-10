# Changing File Ownership
My goal is to read the /flag file, but it's currently owned by root and I don't have permission. For this challenge, I've been given the ability to use the chown command. 
I need to use it to change the owner of /flag to my user, hacker, which will then allow me to read the file and complete the challenge.

## My solve
**Flag:** `pwn.college{Q0jSdHvgLoa5NPsBPNb1LwZDKy3.QXxEjN0wiN4AzNzEzW}`

```hacker@permissions~changing-file-ownership:~$ chown hacker /flag
hacker@permissions~changing-file-ownership:~$ cat /flag
pwn.college{Q0jSdHvgLoa5NPsBPNb1LwZDKy3.QXxEjN0wiN4AzNzEzW}
```

## What I learned
I learned that every file in Linux is owned by a user, which is fundamental to controlling permissions. 
The chown (change owner) command is used to change a file's ownership. By changing the owner of a file to myself, I can gain the permissions necessary to access it, even if it was previously restricted.

## References 
I used the videos available on pwn.college as my references.
