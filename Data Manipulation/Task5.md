# Extracting specific sections of text
This challenge asks us to use the cut command to extract specific characters from a program's output. 
The program /challenge/run gives us data with numbers and the flag characters in a separate column
We need to use cut with the -d " " option to tell it that columns are separated by a space. Then, we'll use the -f option to specify the exact column number that contains the flag characters.
After we get the characters, they will be on separate lines. So, we'll pipe the output to tr -d '\n' to remove the newlines and join all the characters into a single line, revealing the final flag.

## My solve
**Flag:** `pwn.college{ohgLM_2AFbvxtL36ayznnGMXUSM.01NxEzNxwiN4AzNzEzW}`

```
hacker@data~extracting-specific-sections-of-text:~$ /challenge/run | cut -d ' ' -f 2 | tr -d '\n'
pwn.college{ohgLM_2AFbvxtL36ayznnGMXUSM.01NxEzNxwiN4AzNzEzW}
```
## What I learned
I learned how to use the cut command to extract specific columns from a data stream. This is useful for processing structured data where we only need a portion of each line.

## References 
I used the videos and documents available on pwn.college as my references.
