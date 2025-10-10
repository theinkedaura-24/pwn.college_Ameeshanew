# Interrupting processes
This challenge asks us to use the Ctrl-C key combination to get the flag. There's a program named /challenge/run that will not give us the flag until we stop it by sending it an interrupt signal.

To solve this, we just need to run the program and then press Ctrl-C on our keyboard. This will send the necessary signal to the program, causing it to stop and, as a result, give us the flag.

## My solve
**Flag:** `pwn.college{AyWnVbyppWZdtNgJjBLOLOk1Jov.QXzQDO0wiN4AzNzEzW}`

```hacker@processes~interrupting-processes:~$ /challenge/run
I could give you the flag... but I won't, until this process exits. Remember, 
you can force me to exit with Ctrl-C. Try it now!
^C
Good job! You have used Ctrl-C to interrupt this process! Here is your flag:
pwn.college{AyWnVbyppWZdtNgJjBLOLOk1Jov.QXzQDO0wiN4AzNzEzW}
```

## What I learned
I learned a fundamental Linux shortcut for terminating a process: Ctrl-C. This key combination sends an "interrupt" signal to the currently running program in the terminal, which usually causes it to exit.

Unlike the kill command, which requires you to find a program's PID, Ctrl-C is a quick and easy way to stop a program that we just launched directly from our terminal.

For this challenge, I simply ran the /challenge/run program and then pressed Ctrl-C. This action was the specific condition needed to get the flag. It taught me that interrupting a process with Ctrl-C is a common and efficient way to handle programs that are waiting for input or running indefinitely in the foreground.

## References 
I used the videos and documents available on pwn.college as my references.
