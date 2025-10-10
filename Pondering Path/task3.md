# Finding Commands
My goal is to use the which command to find the location of the win executable, then use that path to read a flag file located in the same directory. 
This teaches how to locate programs on the system and use that information.
My task is to find a flag file that's in the same directory as the win command. 
I'll use which win to get the full path to the win executable. Once I know the directory, I'll cat the flag file from that same location to solve the challenge!

## My solve
**Flag:** `pwn.college{QKKbwLjRJ5S4fAFOI72pzWwv-Ye.01NzEzNxwiN4AzNzEzW}}`

type in your solve and your thought process behind solving the challenge. Include as much as info as possible.. Use triple ticks for bash.
```hacker@path~finding-commands:~$ which win
/challenge/paths/24806/win
hacker@path~finding-commands:~$ cat /challenge/paths/24806/flag
pwn.college{QKKbwLjRJ5S4fAFOI72pzWwv-Ye.01NzEzNxwiN4AzNzEzW}
```

## What I learned
I learned that the which command is a super useful tool for finding the exact location of any command on the system. It searches through all the directories in my $PATH variable to pinpoint the executable file. 
This is great for figuring out exactly what program is running when I type a command.

## References 
I used the resources available on pwn.college as my references.
