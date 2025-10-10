# Extracting the first lines with head
This challenge asks us to filter a program's output using the head command. The program /challenge/pwn gives us a lot of text, but we only need to grab the very beginning of it.
To solve this, we are asked to use head -n 7 to get just the first seven lines of the output. We then need to take those seven lines and pass them along to another program, /challenge/college, which will give us the final flag.
Essentially, we are asked to string three commands together with two pipes to perform a sequence of data operations.

## My solve
**Flag:** `pwn.college{0pIc48fH_REgqXm-h2plG0xSVGE.0lNxEzNxwiN4AzNzEzW}`


```hacker@data~extracting-the-first-lines-with-head:~$ /challenge/pwn | head -n 7 | /challenge/college
Congratulations, you piped the right codes!
pwn.college{0pIc48fH_REgqXm-h2plG0xSVGE.0lNxEzNxwiN4AzNzEzW}

```

## What I learned
I learned how to use the head command to select and display a specific number of lines from the beginning of a data stream. 
This is useful when dealing with a program that produces a large amount of output, and you only need the initial part of it.

## References 
I used the videos and documents available on pwn.college as my references.
