# Killing Processes
his challenge asks us to stop a running program using the kill command. 
There's a program called /challenge/dont_run that is already running and is preventing /challenge/run from working.
Our task is to find the Process ID (PID) of the /challenge/dont_run process using a command like ps. 
Once we have its PID, we need to use the kill command with that number to terminate the process. 
After it's stopped, we can then run /challenge/run to get the flag.

## My solve
**Flag:** `pwn.college{M7bM3ALn8-VbkGapSDih3CYvzsA.QXyQDO0wiN4AzNzEzW}`

```hacker@processes~killing-processes:~$ ps -ef | grep /challenge/dont_run
hacker       136     135  0 18:28 ?        00:00:00 /challenge/dont_run
hacker       167     152  0 18:29 pts/0    00:00:00 grep --color=auto /challenge/dont_run
hacker@processes~killing-processes:~$ kill 136
hacker@processes~killing-processes:~$ /challenge/run
Great job! Here is your payment:
pwn.college{M7bM3ALn8-VbkGapSDih3CYvzsA.QXyQDO0wiN4AzNzEzW}

```

## What I learned
I learned how to use the kill command to terminate a running process in Linux. The kill command is a crucial tool for managing system resources and stopping unwanted or misbehaving programs.
The key to using kill is knowing the Process ID (PID) of the program you want to terminate. The PID is a unique number assigned to every running process, which I can find using the ps command (often paired with grep to filter for the specific process I'm looking for).
For this challenge, I first used ps to find the PID of the /challenge/dont_run process. Then, I used kill followed by that PID to terminate the process, which allowed the main program /challenge/run to execute successfully.

## References 
I used the videos and documents available on pwn.college as my references.
