# Exporting Variable
This challenge asks us to set two variables: one as an exported environment variable (PWN=COLLEGE) that child processes can see, and another as a local variable (COLLEGE=PWN) that they cannot.

## My solve
**Flag:** `pwn.college{ALYMJY-t9boYEESd9l8bUlmmi0P.QXyYTN0wiN4AzNzEzW}`


```hacker@variables~exporting-variables:~$ export PWN=COLLEGE
You've set the PWN variable to the proper value!
hacker@variables~exporting-variables:~$ COLLEGE=PWN
You've set the PWN variable to the proper value!
You've set the COLLEGE variable to the proper value!
hacker@variables~exporting-variables:~$ /challenge/run
CORRECT!
You have exported PWN=COLLEGE and set, but not exported, COLLEGE=PWN. Great 
job! Here is your flag:
pwn.college{ALYMJY-t9boYEESd9l8bUlmmi0P.QXyYTN0wiN4AzNzEzW}

```

## What I learned
I learned the difference between local shell variables and environment variables, and that the export command is used to make a variable visible to any new programs we run.

## References 
I used the resources available on pwn.college as my reference.
