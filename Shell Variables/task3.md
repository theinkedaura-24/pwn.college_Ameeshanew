# Multi-word variables
This challenge asks us to set a variable (PWN) to a value that contains a space (COLLEGE YEAH) by using quotes to group the words together.


## My solve
**Flag:** `pwn.college{MZHeuMHJPedkiRmr5EoEfeDBw76.QXwYTN0wiN4AzNzEzW}`


```hacker@variables~multi-word-variables:~$ PWN="COLLEGE YEAH"
You've set the PWN variable properly! As promised, here is the flag:
pwn.college{MZHeuMHJPedkiRmr5EoEfeDBw76.QXwYTN0wiN4AzNzEzW}

```

## What I learned
This challenge asks us to set a variable (PWN) to a value that contains a space (COLLEGE YEAH) by using quotes to group the words together.

I learned that we must enclose values in quotes ("...") when they contain spaces, so the shell treats the entire phrase as a single piece of data.

## References 
I used the resources available on pwn.college as my reference.
