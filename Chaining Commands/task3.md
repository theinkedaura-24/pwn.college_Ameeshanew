# Handling failure
My task is to run /challenge/second, which will only execute after a command fails. I need to use the conditional OR operator (||) to chain it with /challenge/first-failure. 
This ensures that when the first program fails, the second program is triggered as a fallback, which will then give me the flag.

## My solve
**Flag:** `pwn.college{MnME8yVe64IWKu-hQAXkmwYXGfw.01M0MDOxwiN4AzNzEzW}`

```hacker@chaining~handling-failure:~$ /challenge/first-failure || /challenge/second
Nice chaining! Flag: pwn.college{MnME8yVe64IWKu-hQAXkmwYXGfw.01M0MDOxwiN4AzNzEzW}

```

## What I learned
I learned about the conditional OR operator, ||, which executes a second command only if the first command fails (returns a non-zero exit code).
This is the opposite of && and is extremely useful for error handling or defining a fallback action to run when a primary command doesn't succeed.

## References 
I used the resources available on pwn.college as my references.
