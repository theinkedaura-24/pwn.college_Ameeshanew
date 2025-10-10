# Using sudo
The task is to use the modern sudo command to read the flag. We have been granted sudo access, which lets us execute specific commands as the root user. 
We'll need to use this privilege to bypass file permissions and view the contents of the flag file.

## My solve
**Flag:** `pwn.college{k-zPjPVOq1PLQVSHubPVVB7jzK5.QX4UDN1wiN4AzNzEzW}`

```hacker@users~using-sudo:~$ sudo cat not-the-flag
pwn.college{k-zPjPVOq1PLQVSHubPVVB7jzK5.QX4UDN1wiN4AzNzEzW}

```

## What I learned
I learned that sudo is the modern alternative to su. Instead of starting a new root shell, it runs a single command with elevated privileges. 
The key difference is that sudo checks a policy file (/etc/sudoers) to see if my user is authorized, rather than requiring me to know the root user's password.

## References 
I used the resources available on pwn.college as my references.
