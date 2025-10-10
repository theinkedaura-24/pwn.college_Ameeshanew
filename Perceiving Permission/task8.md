# The SUID Bit

My goal is to get a root shell using the /challenge/getroot program. To do this, I must first add the SUID permission bit to the file by running chmod u+s /challenge/getroot. 
After setting the SUID bit, executing the program will grant me a root shell, which I can then use to read the flag.

## My solve
**Flag:** `pwn.college{Q7hyAu9X0cUwDX2i7xmxhs0oUhr.QXzEjN0wiN4AzNzEzW}`

```hacker@permissions~the-suid-bit:~$ chmod u+s /challenge/getroot
hacker@permissions~the-suid-bit:~$ /challenge/getroot
SUCCESS! You have set the suid bit on this program, and it is running as root! 
Here is your shell...
root@permissions~the-suid-bit:~# cat /flag
pwn.college{Q7hyAu9X0cUwDX2i7xmxhs0oUhr.QXzEjN0wiN4AzNzEzW
```

## What I learned
I learned about the SUID (Set User ID) bit, a special permission that makes a program run with the privileges of its owner, not the user executing it. 
This permission appears as an s in the ls -l output and can be set with chmod u+s. 


## References 
I used the resources available on pwn.college as my references.
