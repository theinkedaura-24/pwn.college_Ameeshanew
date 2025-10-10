# Excutable Shell Scripts
My goal is to run a shell script without explicitly typing bash before it. I need to create a script that contains the command /challenge/solve. 
After creating it, I must use chmod to make the script file executable. Finally, I'll run it directly by referencing its path to get the flag.

## My solve
**Flag:** `pwn.college{4pMVky8sHGIVoCYAHZBwMXwWJDf.QX0cjM1wiN4AzNzEzW}}`


```hacker@chaining~executable-shell-scripts:~$ echo "/challenge/solve" > solve.sh && chmod +x solve.sh && ./solve.sh
Congratulations on your shell script execution! Your flag:
pwn.college{4pMVky8sHGIVoCYAHZBwMXwWJDf.QX0cjM1wiN4AzNzEzW}}
```

## What I learned
I learned that I can run a shell script directly if I make the file executable. By adding the execute (x) permission with the chmod command, I can invoke the script by its path (e.g., ./script.sh) instead of having to type bash script.sh.

## References 
I used the resources available on pwn.college as my references.
