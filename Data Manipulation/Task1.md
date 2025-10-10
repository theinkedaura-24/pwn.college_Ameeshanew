# Translating characters
We were tasked with a specific data manipulation problem utilizing the Linux command-line environment. 
The core objective was to extract a flag string from an executable that intentionally changed its output by performing a case-swap on all alphabetic characters (uppercase became lowercase, and vice-versa).

## My solve
**Flag:** `pwn.college{oWTyz2Du2pcyE45NWKEcZ6jnyRX.01MxEzNxwiN4AzNzEzW}`

```pwn.college{oWTyz2Du2pcyE45NWKEcZ6jnyRX.01MxEzNxwiN4AzNzEzW}
hacker@data~translating-characters:~$ /challenge/run | tr 'A-Za-z' 'a-zA-Z'
yOUR CASE-SWAPPED FLAG:
pwn.college{oWTyz2Du2pcyE45NWKEcZ6jnyRX.01MxEzNxwiN4AzNzEzW}
```

## What I learned
I learned how to use the tr command to solve a real-world data manipulation problem. The challenge was to retrieve a flag from a program that purposefully swapped the case of all its characters. For example, 'A' became 'a', and 'b' became 'B'.

My main takeaway was mastering the use of character sets with tr. I learned that you can't just fix one letter at a time; you have to provide tr with a full range of characters to work with.

To solve the challenge, I used two character ranges:

I told tr to find all the uppercase characters from 'A' to 'Z' (A-Z).

I also told it to find all the lowercase characters from 'a' to 'z' (a-z).

Then, I provided the corresponding replacement sets in the opposite order. By sending the program's output through a pipe (|) to tr, I was able to perform this transformation

## References 
I used the documents and videos available on pwn.college as my references.
