# Listing Processes

This challenge asks us to use the ps command to find a hidden program. A special program, which is needed to get the flag, has been started but its filename has been changed to something random, and we can't see it using the ls command.
Our task is to use a useful version of the ps command, like ps -ef or ps aux, to list all the running processes on the system. By looking through this list, we need to find the unique, randomly named process that's already running.
Once we find the filename, we can execute that program ourselves to get the flag.

## My solve
**Flag:** `pwn.college{QDIQu0Oj6hFsrfZ7k4KSk7S6Xg6.QX4MDO0wiN4AzNzEzW}`

```hacker@processes~listing-processes:~$ ps -efww | grep /challenge
root         132       1  0 18:05 ?        00:00:00 /challenge/10151-run-32677
hacker       161     150  0 18:05 pts/0    00:00:00 grep --color=auto /challenge
hacker@processes~listing-processes:~$ /challenge/10151-run-32677
Yahaha, you found me! Here is your flag:
pwn.college{QDIQu0Oj6hFsrfZ7k4KSk7S6Xg6.QX4MDO0wiN4AzNzEzW}

```

## What I learned
I learned that the ps command is used to list currently running processes on a system. While its default output isn't very useful, I learned how to use powerful options to get a full view of all processes. Specifically, I now know two common ways to do this:

ps -ef: This uses the "standard" syntax to list every process in full format. It shows the PID, Parent PID (PPID), and the full command path.

ps aux: This uses the "BSD" syntax to list processes for all users, including those not running in a terminal (x), in a user-oriented format.

The most important thing I learned from this challenge is how to identify a specific process by its command line in the ps output. This is a crucial skill for system administration and security tasks, as it allows you to see what's running on a machine and how it was launched. I also learned the useful trick of using ps ww to prevent the command line from being cut off, ensuring I can read the full path of the process.

## References 
I used the videos and documents available on pwn.college as my references.
