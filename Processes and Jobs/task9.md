# Starting Background Processes
This challenge asks us to run a program in the background right from the start. The program /challenge/run will give us the flag if we launch it in the background instead of the foreground.

To solve this, we just need to add an ampersand (&) to the end of the command. This tells the shell to run the program and immediately return the command prompt to us.

## My solve
**Flag:** `pwn.college{Y5Wuy0MHFThLoi7lMDZQVBDbMA-.QX5QDO0wiN4AzNzEzW}`

```hacker@processes~starting-backgrounded-processes:~$ 


Yay, you started me in the background! Because of that, this text will probably 
overlap weirdly with the shell prompt, but you're used to that by now...

Anyways! Here is your flag!
pwn.college{Y5Wuy0MHFThLoi7lMDZQVBDbMA-.QX5QDO0wiN4AzNzEzW}
^C
[2]+  Done                    /challenge/run

```

## What I learned
What I Learned
I learned a very efficient way to launch a command directly into the background using the & operator. This allows a program to start running immediately without taking over the terminal, freeing me up to run other commands simultaneously.

For this challenge, all I had to do was append an & to the end of the command to launch the program in the background, which was the condition needed to get the flag.

## References 
I used the resources available on pwn.college as my references.
