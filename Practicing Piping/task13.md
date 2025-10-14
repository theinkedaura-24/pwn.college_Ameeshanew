# Split piping stderr and stdout
This challenge asks us to send a program's standard output and standard error to two different programs by combining a standard pipe (|) with file descriptor redirection (2>) and process substitution (>(...)).

## My solve
**Flag:** `pwn.college{YcqjlfRVpuzXdMYs2UJO0jT3jd-.QXxQDM2wiN4AzNzEzW}`

```
hacker@piping~split-piping-stderr-and-stdout:~$ /challenge/hack 2> >(/challenge/the) | /challenge/planet
Congratulations, you have learned a redirection technique that even experts 
struggle with! Here is your flag:
pwn.college{YcqjlfRVpuzXdMYs2UJO0jT3jd-.QXxQDM2wiN4AzNzEzW}

```

## What I learned
I learned how to construct commands that give us complete and separate control over both of a program's output streams, routing each to a different destination in a single line.

## References 
I used the resources available on pwn.college as my references.
