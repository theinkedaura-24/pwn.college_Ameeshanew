# Scripting with Conditionals
My task is to create a script at /home/hacker/solve.sh that uses conditional logic. 
The script must accept one argument and use an if statement to check if that argument is equal to "pwn". 
If the condition is true, it will output "college"; otherwise, it should produce no output. I'll run /challenge/run to verify my script and get the flag.

## My solve
**Flag:** `pwn.college{cw7Y7EakN89O8CJL-4lrsgmQkQJ.0lNzMDOxwiN4AzNzEzW}`
```

type in your solve and your thought process behind solving the challenge. Include as much as info as possible.. Use triple ticks for bash.
```hacker@chaining~scripting-with-conditionals:~$ echo -e '#!/bin/bash\nif [ "$1" == "pwn" ]; then\n\techo "college"\nfi' > /home/hacker/solve.sh
hacker@chaining~scripting-with-conditionals:~$ chmod +x /home/hacker/solve.sh
hacker@chaining~scripting-with-conditionals:~$ /challenge/run
Correct! Your script properly handles all the conditions.
Here's your flag:
pwn.college{cw7Y7EakN89O8CJL-4lrsgmQkQJ.0lNzMDOxwiN4AzNzEzW}
```

## What I learned
I  learned how to add decision-making to my scripts using if statements. The basic syntax is if [ condition ]; then ... fi, and it's important to use spaces correctly around the brackets and end the block with fi. 
This allows my script to check for conditions, like string equality, and execute commands only when those conditions are met.

## References 
I used the resources available on pwn.college as my references.
