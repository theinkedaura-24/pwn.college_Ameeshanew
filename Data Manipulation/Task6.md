# Sorting Data
This challenge asks us to use the sort command to find a hidden flag in a file. The file /challenge/flags.txt contains many fake flags, with the real one mixed in.
We are told that if we sort the file's contents alphabetically, the real flag will end up as the very last line. 
So, we just need to run sort on the file and look at the last line of the output to find the flag.

## My solve
**Flag:** `pwn.college{46zH6aSX4dpLqMaNaW_GQomasA6.0FM0MDOxwiN4AzNzEzW}`

```
hacker@data~sorting-data:~$ sort -r /challenge/flags.txt | head -n 1
pwn.college{46zH6aSX4dpLqMaNaW_GQomasA6.0FM0MDOxwiN4AzNzEzW}

```

## What I learned
I learned how to use the sort command to organize data alphabetically. This is helpful for reordering files or the output of other commands, making it easier to find specific information.
The challenge asked me to find a real flag hidden among 100 fake ones in a file named /challenge/flags.txt. I was given a crucial hint: the real flag would be at the very end after sorting the file alphabetically.
To solve this, I used sort on the flags.txt file. By default, sort organizes lines alphabetically (A to Z), which was exactly what I needed. After sorting, the real flag would be the last line of the output. I could then use other commands like sort -r to grab just that final line.

## References 
I used the videos and documents available on pwn.college as my references.
