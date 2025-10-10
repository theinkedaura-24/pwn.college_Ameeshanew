# Scripting with Default Cases
My task is to create a script at /home/hacker/solve.sh that uses an if-else block. 
It will check if the first argument is "pwn" and output "college" if it matches. 
For any other argument, the else block should trigger and output "nope". I'll run /challenge/run to verify the script and get my flag.


## My solve
**Flag:** `pwn.college{QyWN708xfkh8xPpen3cJ9SKPMDt.01NzMDOxwiN4AzNzEzW}`
```hacker@chaining~scripting-with-default-cases:~$ echo -e '#!/bin/bash\nif [ "$1" == "pwn" ]; then\n\techo "college"\nelse\n\techo "nope"\nfi' > /home/hacker/solve.sh
hacker@chaining~scripting-with-default-cases:~$ chmod +x /home/hacker/solve.sh
hacker@chaining~scripting-with-default-cases:~$ /challenge/run
Correct! Your script properly handles the if/else conditions.
Here's your flag:
pwn.college{QyWN708xfkh8xPpen3cJ9SKPMDt.01NzMDOxwiN4AzNzEzW}
```

## What I learned
I  learned how to use the else clause to create a fallback action for an if statement. This allows my script to handle cases where the initial condition is false, providing a default behavior. 
The full if ... then ... else ... fi structure lets me define actions for both the "true" and "false" paths of a condition.

## References 
I used the resources available on pwn.college as my references.
