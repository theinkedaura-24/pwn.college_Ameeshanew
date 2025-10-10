# Building on Success
My task is to successfully run two programs, /challenge/first-success and /challenge/second. They won't produce the flag if run separately, so I must chain them with the conditional AND operator (&&). 
This ensures that the second program will only execute if the first one completes successfully, which is the necessary condition to get the flag.

## My solve
**Flag:** `pwn.college{8dprDEtWKY8tnKBt3O4yFb6YlAF.0lM0MDOxwiN4AzNzEzW}`

```hacker@chaining~building-on-success:~$ /challenge/first-success && /challenge/second
Nice chaining! Flag: pwn.college{8dprDEtWKY8tnKBt3O4yFb6YlAF.0lM0MDOxwiN4AzNzEzW}

```

## What I learned
I  learned about the conditional AND operator, &&, which chains commands together based on success. It executes the second command only if the first command completes successfully (meaning it has an exit code of 0). 
This is a powerful tool for creating reliable command sequences where one step depends on the success of the previous one.

## References 
I used the resources available on pwn.collge as my references.
