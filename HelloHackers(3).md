# Command History
The challenge asks us to invoke a previously used command by accessing the command history using the up and down arrow keys.

## My solve
**Flag:** `hacker@hello~command-history:~$ the flag is pwn.college{ACbvNDeCcyK-_krQrNS3P-FOCjq.0lNzEzNxwiN4AzNzEzW}`
```bash: the: command not found```
I used the up arrow key to use the previously used command and got my flag from the same.


## What I learned
I learnt that the shell saves a history of every command we invoke, and we can scroll through those saved commands with the up/down arrow keys.


## References 
I used the video on pwn.college as my reference.
