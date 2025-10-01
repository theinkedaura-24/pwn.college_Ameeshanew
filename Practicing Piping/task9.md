# Filtering with grep -v
This challenge asks us to find a real flag by using grep -v to filter out thousands of decoy lines that all contain the word "DECOY".

## My solve
**Flag:** `pwn.college{0rzI3-iFjwrBGdLpiUpW9-higgn.0FOxEzNxwiN4AzNzEzW}`


```
hacker@piping~filtering-with-grep-v:~$ /challenge/run | grep -v DECOY
pwn.college{0rzI3-iFjwrBGdLpiUpW9-higgn.0FOxEzNxwiN4AzNzEzW}

```

## What I learned
I learned that the -v option inverts grep's behavior, causing it to print only the lines that do not match a pattern, which is a powerful way to remove known "noise" from data.

## References 
I used the resources available on pwn.college as my reference.
