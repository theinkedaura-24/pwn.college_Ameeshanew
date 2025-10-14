# Writing to multiple programs
This challenge asks us to use tee combined with output process substitution (>(...)) to take the output from one command and feed it as simultaneous input to two other commands.

## My solve
**Flag:** `pwn.college{8z4GFhHVgeVl4lWOVharNZjiWhu.QXwgDN1wiN4AzNzEzW}`

```
hacker@piping~split-piping-stderr-and-stdout:~$ /challenge/hack 2> >(/challenge/the) | /challenge/planet
Congratulations, you have learned a redirection technique that even experts 
struggle with! Here is your flag:
pwn.college{YcqjlfRVpuzXdMYs2UJO0jT3jd-.QXxQDM2wiN4AzNzEzW}

```

## What I learned
I learned that output process substitution (>(...)) make a command's standard input look like a file, which allows tools like tee to write directly to other programs.

## References 
I used the resources available on pwn.college as my reference.
