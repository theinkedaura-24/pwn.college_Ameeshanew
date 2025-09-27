# cat:not the pet, but the command
Read the flag file in the home directory using the cat command.

## My solve
**Flag**: `pwn.college{kvH9e1s-VpaTqJTSuxptpXflwVs.QXxcTN0wiN4AzNzEzW}`

I started in my shell where the challenge had placed the flag into the standard flag file in my home environment. The challenge description hinted that the flag would be copied into the flag file in my home directory and suggested using cat to read it.

I used cat to print the contents of /flag (the standard path used in these challenges). The sequence I ran and the output I saw is below:
```
hacker@commands~cat-not-the-pet-but-the-command:~$ cat /flag
pwn.college{kvH9e1s-VpaTqJTSuxptpXflwVs.QXxcTN0wiN4AzNzEzW}
```

**## What I learned**
cat is a simple and powerful command for reading file contents. It prints the full contents of files to standard output and can concatenate multiple files if given multiple arguments.

**## References**
Videos and documents available on pwn.college 
