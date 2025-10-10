# Backgrounding processes
This challenge asks us to use the bg command to run a program in the background. The program /challenge/run needs to see another copy of itself running on the same terminal, but not suspended, to give us the flag.

To solve this, we need to:

Run the /challenge/run program once.

Press Ctrl-Z to suspend it.

Type bg and press Enter to resume it in the background.

Run /challenge/run a second time, which will now find the first copy running in the background and give us the flag.

## My solve
**Flag:** `pwn.college{oTShGUl9JIzuANfjEPfYOGPaG3A.QX3QDO0wiN4AzNzEzW}`

```hacker@processes~backgrounding-processes:~$ /challenge/run
I'll only give you the flag if there's already another copy of me running *and 
not suspended* in this terminal... Let's check!

UID          PID STAT CMD
root         175 S+   bash /challenge/run
root         177 R+   ps -o user=UID,pid,stat,cmd

I don't see a second me!

To pass this level, you need to suspend me, resume the suspended process in the 
background, and then launch a new version of me! You can background me with 
Ctrl-Z (and resume me in the background with 'bg') or, if you're not ready to 
do that for whatever reason, just hit Enter and I'll exit!
^Z
[1]+  Stopped                 /challenge/run
hacker@processes~backgrounding-processes:~$ bg
[1]+ /challenge/run &
hacker@processes~backgrounding-processes:~$ 


Yay, I'm now running the background! Because of that, this text will probably 
overlap weirdly with the shell prompt. Don't panic; just hit Enter a few times 
to scroll this text out.
/challenge/run
I'll only give you the flag if there's already another copy of me running *and 
not suspended* in this terminal... Let's check!

UID          PID STAT CMD
root         175 S    bash /challenge/run
root         185 S    sleep 6h
root         186 S+   bash /challenge/run
root         188 R+   ps -o user=UID,pid,stat,cmd

Yay, I found another version of me running in the background! Here is the flag:
pwn.college{oTShGUl9JIzuANfjEPfYOGPaG3A.QX3QDO0wiN4AzNzEzW}

```

## What I learned
I learned that the bg command is used to resume a suspended process in the background. This is different from fg, which brings the process to the foreground. Running a process in the background allows it to continue its work without occupying the terminal, so I can immediately run other commands.

For this challenge, I needed to have two copies of /challenge/run active at the same time and in the same terminal. To do this, I:

Launched /challenge/run.

Suspended it using Ctrl-Z.

Resumed the suspended process in the background using bg.

Immediately launched /challenge/run a second time.

The second instance of the program detected the first one running in the background, fulfilling the condition to get the flag. This taught me a powerful method for multitasking on the command line, allowing me to run multiple processes simultaneously.

## References 
I used the documents and videos available on pwn.college as my references.
