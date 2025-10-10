# Other users with su
My goal for this challenge is to switch to the zardus user account. I need to use the su command with zardus as the argument and enter the password dont-hack-me. 
Once I am logged in as zardus, I must run the /challenge/run executable to get the flag.

## My solve
**Flag:** `pwn.college{I5Wj-nw1rbH2-XI_TZ1Nwo2sFRs.QX2UDN1wiN4AzNzEzW}`

```hacker@users~other-users-with-su:~$ su zardus
Password: 
zardus@users~other-users-with-su:/home/hacker$ /challenge/run
Congratulations, you have become Zardus! Here is your flag:
pwn.college{I5Wj-nw1rbH2-XI_TZ1Nwo2sFRs.QX2UDN1wiN4AzNzEzW}

```

## What I learned
I  learned that the su command is not just for becoming root. By providing a username as an argument (e.g., su zardus), I can switch to that specific user's account. The command still requires the password for the target user to successfully authenticate the switch.

## References 
I used the resources available on pwn.college as my references.
