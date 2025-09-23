# Position thy self
This challenge will require you to execute the /challenge/run program from a specific path. 

## My solve
**Flag:** `pwn.college{4prRX3bSRj_FsZdc1J3cDSA0BWR.QX2QTN0wiN4AzNzEzW}}`

First, I input /challenge/run, which gives me the following message :
Incorrect...
You are not currently in the /sys/kernel directory.
Please use the `cd` utility to change the directory appropriately.
So now I know in which directory /challenge/run is in, so I run :
cd /sys/kernel to change the directory and when that happens successfully, I run :
/challenge/run, which gives me my flag.

## What I learned
I learned how to navigate between directories using the cd command and passing a path to it as an argument.

## References 
I used the video available on pwn.college as my reference.
