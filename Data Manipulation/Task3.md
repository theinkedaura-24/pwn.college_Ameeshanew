# Deleting newlines
The challenge involved a program that intentionally inserted newline characters (\n) into a flag string. 
Our task was to use the tr utility with its -d (delete) flag to remove these newline characters.
The solution leveraged the escaped character \n within quotes to correctly target and delete the line separators from the data 

## My solve
**Flag:** `pwn.college{kazu_d4GyZeRRbG2qML29MfDRID.0VNxEzNxwiN4AzNzEzW}`

```
hacker@data~deleting-newlines:~$ /challenge/run | tr -d '\n'
Your line-split flag: pwn.college{kazu_d4GyZeRRbG2qML29MfDRID.0VNxEzNxwiN4AzNzEzW}
```


## What I learned
I learned how to use the tr command to delete newline characters (\n) from a data stream. The challenge required me to take a flag that was broken up by multiple newlines and transform it into a single, continuous line of text.

To accomplish this, I used the -d flag for deletion along with the special escaped character \n. I learned that \n represents a newline and must be enclosed in quotes to prevent the shell from interpreting the newline command before it reaches tr

## References 
I used the videos and documents available on pwn.college as my references.
