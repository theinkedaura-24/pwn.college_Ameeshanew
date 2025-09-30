# Searching for manuals
This challenge hides the manpage for the challenge by randomizing its name. But, all of the manpages are gathered in a searchable database, so we'll be able to search the man page database to find the hidden challenge man page!
To figure out how to search for the right manpage, read the man page manpage by doing: man man!

## My solve
**Flag:** `pwn.college{U04i3PQGsxvVx8RfaLgsOYjEur-.QX2EDO0wiN4AzNzEzW}`


```hacker@man~searching-for-manuals:~$ man man
hacker@man~searching-for-manuals:~$ man -k challenge
isxvxfagsj (1)       - print the flag!
hacker@man~searching-for-manuals:~$ man isxvxfagsj
hacker@man~searching-for-manuals:~$ /challenge/challenge --isxvxf 043
Correct usage! Your flag: pwn.college{U04i3PQGsxvVx8RfaLgsOYjEur-.QX2EDO0wiN4AzNzEzW}

```

## What I learned
I learned how to use the man man command to read the man page manpage.

## References 
I used the resources available on pwn.college as my reference.
