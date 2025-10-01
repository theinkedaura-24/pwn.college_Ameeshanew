# Duplicating piped data with tee
This challenge asks us to use the tee command to "spy" on the data flowing through a pipe between two programs, saving a copy to a file for inspection.

## My solve
**Flag:** `pwn.college{0nezpG5qGSnV43WMuwKD1-bCbLt.QXxITO0wiN4AzNzEzW}`


```hacker@piping~duplicating-piped-data-with-tee:~$ cat /challenge/pwn
#!/opt/pwn.college/bash

echo "Processing..." > /dev/tty
sleep 1
DIR=$(/bin/dirname ${BASH_SOURCE[0]})
if ! $DIR/chio.py --check_stdout_pipe tee 2>/dev/null && ! $DIR/chio.py --check_stdout_pipe challenge_shellscript 2>/dev/null
then
        fold -s <<< "You must pipe the output of /challenge/pwn into /challenge/college (or 'tee' for debugging)." > /dev/tty
        exit 1
fi

SECRET_VAL=$(tail -c+13 /flag | head -c8)
if [ "$1" != "--secret" ] || [ "$2" != "$SECRET_VAL" ]
then
        echo "Usage: $0 --secret [SECRET_ARG]"
        echo ""
        echo "SECRET_ARG should be \"$SECRET_VAL\""
        sleep 8
        exit 2
fi

echo "Correct! Passing secret value to /challenge/college..." > /dev/tty
echo "SUPERSECRET:$(tail -c+20 /flag | head -c4)"
sleep 8
hacker@piping~duplicating-piped-data-with-tee:~$ /challenge/pwn --secret 0nezpG5q | tee pwn | /challenge/college
Processing...
WARNING: you are overwriting file pwn with tee's output...
Correct! Passing secret value to /challenge/college...
Great job! Here is your flag:
pwn.college{0nezpG5qGSnV43WMuwKD1-bCbLt.QXxITO0wiN4AzNzEzW}

```

## What I learned
I learned that tee acts like a T-splitter for shell pipes, letting us view or save data as it passes from one command to the next without interrupting the pipeline, which is invaluable for debugging.

## References 
I used the resources available on pwn.college as my reference.
