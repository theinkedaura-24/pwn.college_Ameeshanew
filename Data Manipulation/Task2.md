# Deleting characters
Professional Challenge Description
We were tasked with a data filtering problem using the Linux command line. 
The objective was to sanitize a data stream by removing specific, unwanted characters (^ and %) that were interspersed among a valid string. 
This required us to use the tr utility with its -d (delete) flag. 

## My solve
**Flag:** `pwn.college{YKtBLccnE7GnxhBw-HYUcmPOgUK.0FNxEzNxwiN4AzNzEzW}`

```
hacker@data~deleting-characters:~$ /challenge/run | tr -d '^%'
Your character-stuffed flag:
pwn.college{YKtBLccnE7GnxhBw-HYUcmPOgUK.0FNxEzNxwiN4AzNzEzW}
```


## What I learned
Professional Challenge Description
We were tasked with a data filtering problem using the Linux command line. 
The objective was to sanitize a data stream by removing specific, unwanted characters (^ and %) that were interspersed among a valid string. 
This required us to use the tr utility with its -d (delete) flag. The task was successfully completed by piping the program's output to tr and providing the precise list of characters to be deleted, thereby isolating the clean, readable flag from the surrounding noise.

What I Learned
I learned that tr is not just for translating characters; it can also be used to delete them from a stream. The -d flag is the key to this functionality. I discovered that you can specify multiple characters to be deleted in a single command, making it an efficient tool for data cleanup.

## References 
I used the videos and documents available on pwn.college as my references.
