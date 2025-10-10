# Resuming Processes
This challenge asks us to suspend a program and then resume it to get the flag. The program /challenge/run needs to be suspended and then brought back to the foreground to complete its task.
To solve this, we need to:
Run the /challenge/run program.
Press Ctrl-Z to suspend it and get our terminal prompt back.
Type fg and press Enter to resume the program, which will give us the flag.

## My solve
**Flag:** `pwn.college{cStTA42Dpwtyfm_EOLaKnaosQ7m.QX2QDO0wiN4AzNzEzW}`

```hacker@processes~resuming-processes:~$ /challenge/run
Let's practice resuming processes! Suspend me with Ctrl-Z, then resume me with 
the 'fg' command! Or just press Enter to quit me!
^Z
[2]+  Stopped                 /challenge/run
hacker@processes~resuming-processes:~$ fg
/challenge/run
I'm back! Here's your flag:
pwn.college{cStTA42Dpwtyfm_EOLaKnaosQ7m.QX2QDO0wiN4AzNzEzW}
Don't forget to press Enter to quit me!

```

## What I learned
I learned that the fg command is used to resume a suspended process. This command, which stands for "foreground," brings a process that was paused with Ctrl-Z back to life and puts it back in control of the terminal.

The challenge required me to use both Ctrl-Z and fg in sequence. I first ran the /challenge/run program, then suspended it with Ctrl-Z. Once my command prompt returned, I used fg to resume the process, which was the condition needed to get the flag.

This taught me the full workflow for temporarily managing a process in the terminal: suspending it to the background with Ctrl-Z and then resuming it with fg.

## References 
I used the videos and documents available on pwn.college as my references.
