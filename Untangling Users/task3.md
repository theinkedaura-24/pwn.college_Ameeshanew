# Cracking 
In this challenge, a password file has been leaked and is located at /challenge/shadow-leak. Our job is to use the John the Ripper tool to crack the password for the zardus user from this file. 
After finding the password, I'll su to zardus and run /challenge/run to capture the flag.

## My solve
**Flag:** `pwn.college{weAI58O01yq9Q_x1Ja-vHLqKyNq.QX3UDN1wiN4AzNzEzW}`

```	hacker@users~cracking-passwords:~$ john /challenge/shadow-leak
Loaded 1 password hash (crypt, generic crypt(3) [?/64])
Press 'q' or Ctrl-C to abort, almost any other key for status
0g 0:00:00:07 76% 1/3 0g/s 282.3p/s 282.3c/s 282.3C/s 9999945..Z9999932
aardvark         (zardus)
1g 0:00:00:20 100% 2/3 0.04870g/s 283.6p/s 283.6c/s 283.6C/s Johnson..buzz
Use the "--show" option to display all of the cracked passwords reliably
Session completed
hacker@users~cracking-passwords:~$ --show
bash: --show: command not found
hacker@users~cracking-passwords:~$ john --show /challenge/shadow-leak
hacker:NO PASSWORD:20357:0:99999:7:::
zardus:aardvark:20371:0:99999:7:::

2 password hashes cracked, 0 left
hacker@users~cracking-passwords:~$ su zardus
Password: 
zardus@users~cracking-passwords:/home/hacker$ /challenge/run
Congratulations, you have become Zardus! Here is your flag:
pwn.college{weAI58O01yq9Q_x1Ja-vHLqKyNq.QX3UDN1wiN4AzNzEzW}

```
## What I learned
 I learned that Linux stores password hashes in the /etc/shadow file, which is normally only readable by root. 
 If this file leaks, an attacker can use a tool like John the Ripper to perform an offline password cracking attack. 
 The tool repeatedly guesses and hashes passwords, comparing them against the leaked hashes until it finds a match.
 
## References 
I used the resources available on pwn.college as my references..
