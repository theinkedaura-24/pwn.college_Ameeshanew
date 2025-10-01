# Grepping live output
This challenge asks us to use the pipe operator (|) to send the output of /challenge/run directly to the grep command to find the flag in a single step.

## My solve
**Flag:** `pwn.college{0zo2EOqzxhV-lek2YcvghMAKcq0.QX5EDO0wiN4AzNzEzW}`


```hacker@piping~grepping-live-output:~$ /challenge/run | grep pwn.college
[INFO] WELCOME! This challenge makes the following asks of you:
[INFO] - the challenge checks for a specific process at the other end of stdout : grep
[INFO] - the challenge will output a reward file if all the tests pass : /challenge/.data.txt

[HYPE] ONWARDS TO GREATNESS!

[INFO] This challenge will perform a bunch of checks.
[INFO] If you pass these checks, you will receive the /challenge/.data.txt file.

[TEST] You should have redirected my stdout to another process. Checking...
[TEST] Performing checks on that process!

[INFO] The process' executable is /nix/store/8b4vn1iyn6kqiisjvlmv67d1c0p3j6wj-gnugrep-3.11/bin/grep.
[INFO] This might be different than expected because of symbolic links (for example, from /usr/bin/python to /usr/bin/python3 to /usr/bin/python3.8).
[INFO] To pass the checks, the executable must be grep.

[PASS] You have passed the checks on the process on the other end of my stdout!
[PASS] Success! You have satisfied all execution requirements.
pwn.college{0zo2EOqzxhV-lek2YcvghMAKcq0.QX5EDO0wiN4AzNzEzW}

```

## What I learned
I learned how the pipe (|) connects the standard output of one command to the standard input of another, allowing us to build  command pipelines that process data without needing temporary files.

## References 
I used the resources available on pwn.college as my reference.
