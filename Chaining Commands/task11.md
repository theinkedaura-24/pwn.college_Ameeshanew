# Scripting with Multiple Conditions
My task is to create a script at /home/hacker/solve.sh that handles multiple different inputs. 
I need to use an if-elif-else structure to check for three specific arguments ("hack", "pwn", and "learn") and provide a unique output for each, with a default "unknown" response for all other inputs. 
I'll run /challenge/run to verify my script and get the flag.

## My solve
**Flag:** `pwn.college{gq76FY2j5fO2YVKmytfwUANV0K8.0FOzMDOxwiN4AzNzEzW}`

```hacker@chaining~scripting-with-multiple-conditions:~$ echo -e '#!/bin/bash\nif [ "$1" == "hack" ]; then\n\techo "the planet"\nelif [ "$1" == "pwn" ]; then\n\techo "college"\nelif [ "$1" == "learn" ]; then\n\techo "linux"\nelse\n\techo "unknown"\nfi' > /home/hacker/solve.sh
hacker@chaining~scripting-with-multiple-conditions:~$ chmod +x /home/hacker/solve.sh
hacker@chaining~scripting-with-multiple-conditions:~$ /challenge/run
Correct! Your script properly handles all the conditions with elif.
Here's your flag:
pwn.college{gq76FY2j5fO2YVKmytfwUANV0K8.0FOzMDOxwiN4AzNzEzW}
```

## What I learned
I learned how to check a sequence of different conditions using the elif (else if) statement. This allows me to create a chain of checks to handle multiple specific cases before falling back to a final else block, making my conditional logic much cleaner and more powerful than just a single if-else.

## References 
I used the resources available on pwn.college as my references.
