# Multiple options for tab completion
This challenge asks us to use tab completion to find a specific file when multiple filenames share a common prefix.


## My solve
**Flag:** `pwn.college{QYBwJCPy0umjeat8sO9WGU52IE0.0lN0EzNxwiN4AzNzEzW}`

```
hacker@globbing~multiple-options-for-tab-completion:~$ cat /challenge/files/pwn
pwn                    pwn-the-planet         pwncollege-flag        pwncollege-flyswatter  
pwn-college            pwncollege-family      pwncollege-flamingo    pwncollege-hacking     
hacker@globbing~multiple-options-for-tab-completion:~$ cat /challenge/files/pwncollege-flag
pwn.college{QYBwJCPy0umjeat8sO9WGU52IE0.0lN0EzNxwiN4AzNzEzW}
```

## What I learned
I learned that when a Tab press has multiple options, the first press completes the common part, and a second press lists all possibilities, allowing us to discover the correct filename.

## References 
I used the resources available on pwn.college as my references.
