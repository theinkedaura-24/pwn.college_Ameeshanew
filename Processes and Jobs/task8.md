# Foregrounding processes
This challenge asks us to use the fg command to bring a program from the background to the foreground. The program /challenge/run will only give us the flag if we run it, send it to the background, and then bring it back to the foreground.

To solve this, we need to:

Run /challenge/run.

Press Ctrl-Z to suspend it.

Type bg to run it in the background.

Type fg to bring it back to the foreground, which will give us the flag.

## My solve
**Flag:** `pwn.college{4L8ZcEc-Op3e3nVTmCPSzVLBOdV.QX4QDO0wiN4AzNzEzW}`

```hacker@processes~foregrounding-processes:~$ /challenge/run
To pass this level, you need to suspend me, resume the suspended process in the 
background, and *then* foreground it without re-suspending it! You can 
background me with Ctrl-Z (and resume me in the background with 'bg') or, if 
you're not ready to do that for whatever reason, just hit Enter and I'll exit!
^Z
[1]+  Stopped                 /challenge/run
hacker@processes~foregrounding-processes:~$ fg
/challenge/run
You resumed me into the foreground from suspension! Please resume me into the 
background, and *then* swap me into the foreground directly from there (or 
press Enter, and I'll exit).
bg
hacker@processes~foregrounding-processes:~$ fg
bash: fg: current: no such job
hacker@processes~foregrounding-processes:~$ /challenge/run
To pass this level, you need to suspend me, resume the suspended process in the 
background, and *then* foreground it without re-suspending it! You can 
background me with Ctrl-Z (and resume me in the background with 'bg') or, if 
you're not ready to do that for whatever reason, just hit Enter and I'll exit!
^Z
[1]+  Stopped                 /challenge/run
hacker@processes~foregrounding-processes:~$ bg
[1]+ /challenge/run &
hacker@processes~foregrounding-processes:~$ 


Yay, I'm now running the background! Because of that, this text will probably 
overlap weirdly with the shell prompt. Don't panic; just hit Enter a few times 
to scroll this text out. After that, resume me into the foreground with 'fg'; 
I'll wait.
fg
/challenge/run
YES! Great job! I'm now running in the foreground. Hit Enter for your flag!

pwn.college{4L8ZcEc-Op3e3nVTmCPSzVLBOdV.QX4QDO0wiN4AzNzEzW}

```

## What I learned
I learned that the fg command can be used to bring a backgrounded process back into the foreground. This is the same command used for suspended processes, showing that fg is the go-to tool for regaining control of a process that's not actively running in the terminal.

The challenge required me to:

Run /challenge/run.

Use Ctrl-Z to suspend it.

Use bg to resume it in the background.

Use fg to bring it back to the foreground, which was the condition needed to get the flag.

This taught me the complete cycle of managing processes in a single terminal: launching them, suspending them, backgrounding them, and bringing them back to the foreground.

## References 
I used the resources available on pwn.college as my references..
