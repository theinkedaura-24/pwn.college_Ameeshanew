# Printing Exported Variables
This challenge asks us to use the env command to list all current environment variables and find the FLAG variable within its output.

## My solve
**Flag:** `pwn.college{MZzLnLWdX4H2IFANCqOrVy0MIy_.QX4UTN0wiN4AzNzEzW}`

```hacker@variables~printing-exported-variables:~$ env
SHELL=/run/dojo/bin/bash
HOSTNAME=variables~printing-exported-variables
PWD=/home/hacker
MANPATH=/run/dojo/share/man:
DOJO_AUTH_TOKEN=e4843db869d5d8dd53690425838c0b6c487f1ba676605f1adb25e12cb420607c
HOME=/home/hacker
LANG=C.UTF-8
FLAG=pwn.college{MZzLnLWdX4H2IFANCqOrVy0MIy_.QX4UTN0wiN4AzNzEzW}
TERMINFO=/run/dojo/share/terminfo
TERM=xterm-256color
SHLVL=2
LC_CTYPE=C.UTF-8
SSL_CERT_FILE=/run/dojo/etc/ssl/certs/ca-bundle.crt
PATH=/run/challenge/bin:/run/dojo/bin:/root/.cargo/bin:/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin
DEBIAN_FRONTEND=noninteractive
_=/run/dojo/bin/env

```

## What I learned
I learned that the env command is a tool for displaying all exported environment variables, giving us a clear view of the data being passed to child processes.

## References 
I used the resources available on pwn.college as my references.
