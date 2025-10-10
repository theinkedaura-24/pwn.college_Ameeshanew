# Suspending processes
This challenge asks us to use the Ctrl-Z command to solve it. The program /challenge/run needs to see another copy of itself running on the same terminal to give us the flag.

To solve this, we need to:

Run the /challenge/run program.

Press Ctrl-Z to suspend it. This will give us our command prompt back while keeping the first program paused in the background.

Run /challenge/run a second time. This second copy will detect the first, suspended copy and give us the flag.

## My solve
**Flag:** `pwn.college{wZMSFbunO8Jm47lh0334HbGl26m.QX1QDO0wiN4AzNzEzW}`

```5.	hacker@processes~suspending-processes:~$ /challenge/run
I'll only give you the flag if there's already another copy of me running in 
this terminal... Let's check!

UID          PID    PPID  C STIME TTY          TIME CMD
root         175     155  0 19:09 pts/0    00:00:00 bash /challenge/run
root         177     175  0 19:09 pts/0    00:00:00 ps -f

I don't see a second me!

To pass this level, you need to suspend me and launch me again! You can 
background me with Ctrl-Z or, if you're not ready to do that for whatever 
reason, just hit Enter and I'll exit!
^Z
[1]+  Stopped                 /challenge/run
hacker@processes~suspending-processes:~$ /challenge/run
I'll only give you the flag if there's already another copy of me running in 
this terminal... Let's check!

UID          PID    PPID  C STIME TTY          TIME CMD
root         175     155  0 19:09 pts/0    00:00:00 bash /challenge/run
root         182     155  0 19:10 pts/0    00:00:00 bash /challenge/run
root         184     182  0 19:10 pts/0    00:00:00 ps -f

Yay, I found another version of me! Here is the flag:
pwn.college{wZMSFbunO8Jm47lh0334HbGl26m.QX1QDO0wiN4AzNzEzW}

Goodbye!
```

## What I learned
I learned that Ctrl-Z is a useful key combination for suspending a running process. Unlike Ctrl-C, which terminates a process, Ctrl-Z temporarily pauses the program and moves it to the background. This allows you to get your terminal prompt back without killing the process entirely.

The challenge required me to use this skill to launch two copies of the same program (/challenge/run) in the same terminal. To do this, I first launched /challenge/run, then pressed Ctrl-Z to suspend it. With my terminal prompt returned, I was able to launch a second copy of the program, which then detected the first suspended copy and gave me the flag.

This taught me a basic but powerful technique for managing processes in the terminal: suspending them to free up the command line for other tasks while keeping the original process alive in the background.

## References 
I used the videos and documents available on pwn.college as my references.
