# grepping for a needle in a hay
Using grep to find the flag in a very large file

## My solve
**Flag:** `pwn.college{8J9Uvmf50Pa9gU8geXYQce6Cr_5.QX3EDO0wiN4AzNzEzW}`

The challenge places the flag somewhere inside a very large file with a lot of lines, so opening the entire file is impractical.

The hint tells us the flag always starts with pwn.college, so we can search for that exact substring.

grep is perfect for this: grep SEARCH_STRING /path/to/file prints only lines that contain the search string.

I ran grep for pwn.college against the file and it returned the single line containing the full flag.

```
hacker@commands~grepping-for-a-needle-in-a-haystack:~$ grep pwn.college /challenge/data.txt
pwn.college{8J9Uvmf50Pa9gU8geXYQce6Cr_5.QX3EDO0wiN4AzNzEzW}

```

## What I learned
grep is the right tool to extract relevant lines from very large files quickly. It is much faster and less noisy than opening the file in an editor.

When we know a substring of the file we're looking for we can search for that exact substring to reduce false positives.

## References 
I used the video and document available on pwn.college as my references.
