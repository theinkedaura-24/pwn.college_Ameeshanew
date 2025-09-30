# An Epic Filesystem Quest
In this challenge, the flag is hidden! Here, we used ls and cat to follow breadcrumbs and find it! Here's how it worked

Your first clue is in /. Head on over there.
Look around with ls. There'll be a file named HINT or CLUE or something along those lines!
cat that file to read the clue!
Depending on what the clue says, head on over to the next directory (or don't!).
Follow the clues to the flag!

## My solve
**Flag:** `pwn.college{AeLXxRBhxnLiGlSiKhWAny47tQc.QX5IDO0wiN4AzNzEzW}`

```11.	hacker@commands~an-epic-filesystem-quest:~$ cd /
hacker@commands~an-epic-filesystem-quest:/$ ls
CUE  bin  boot  challenge  dev  etc  flag  home  lib  lib32  lib64  libx32  media  mnt  nix  opt  proc  root  run  sbin  srv  sys  tmp  usr  var
hacker@commands~an-epic-filesystem-quest:/$ cat CUE
Great sleuthing!
The next clue is in: /opt/busybox/busybox-1.33.2/include/config/feature/init/modify

Watch out! The next clue is **trapped**. You'll need to read it out without 'cd'ing into the directory; otherwise, the clue will self destruct!
hacker@commands~an-epic-filesystem-quest:/$ ls /opt/busybox/busybox-1.33.2/include/config/feature/init/modify
CLUE-TRAPPED  cmdline.h
hacker@commands~an-epic-filesystem-quest:/$ cat CLUE-TRAPPED
cat: CLUE-TRAPPED: No such file or directory
hacker@commands~an-epic-filesystem-quest:/$ cat opt/busybox/busybox-1.33.2/include/config/feature/init/modify CLUE-TRAPPED
cat: opt/busybox/busybox-1.33.2/include/config/feature/init/modify: Is a directory
cat: CLUE-TRAPPED: No such file or directory
hacker@commands~an-epic-filesystem-quest:/$ cat opt/busybox/busybox-1.33.2/include/config/feature/init/modify/CLUE-TRAPPED
Lucky listing!
The next clue is in: /opt/linux/linux-5.4/Documentation/devicetree/bindings/arc

Watch out! The next clue is **trapped**. You'll need to read it out without 'cd'ing into the directory; otherwise, the clue will self destruct!
hacker@commands~an-epic-filesystem-quest:/$ ls /opt/linux/linux-5.4/Documentation/devicetree/bindings/arc
BLUEPRINT-TRAPPED  archs-pct.txt  axs101.txt  axs103.txt  eznps.txt  hsdk.txt  pct.txt
hacker@commands~an-epic-filesystem-quest:/$ cat /opt/linux/linux-5.4/Documentation/devicetree/bindings/arc/ BLUEPRINT-TRAPPED
cat: /opt/linux/linux-5.4/Documentation/devicetree/bindings/arc/: Is a directory
cat: BLUEPRINT-TRAPPED: No such file or directory
hacker@commands~an-epic-filesystem-quest:/$ cat /opt/linux/linux-5.4/Documentation/devicetree/bindings/arc/BLUEPRINT-TRAPPED
Congratulations, you found the clue!
The next clue is in: /opt/linux/linux-5.4/fs/notify/fanotify

The next clue is **hidden** --- its filename starts with a '.' character. You'll need to look for it using special options to 'ls'.
hacker@commands~an-epic-filesystem-quest:/$ ls -a /opt/linux/linux-5.4/fs/notify/fanotify
.  ..  .EVIDENCE  .built-in.a.cmd  Kconfig  Makefile  built-in.a  fanotify.c  fanotify.h  fanotify_user.c
hacker@commands~an-epic-filesystem-quest:/$ cat /opt/linuxx/linux-5.4/fs/notify/fanotify/.EVIDENCE
cat: /opt/linuxx/linux-5.4/fs/notify/fanotify/.EVIDENCE: No such file or directory
hacker@commands~an-epic-filesystem-quest:/$ cat /opt/linux/linux-5.4/fs/notify/fanotify/.EVIDENCE
Great sleuthing!
The next clue is in: /opt/busybox/busybox-1.33.2/include/config/unicode/neutral

The next clue is **delayed** --- it will not become readable until you enter the directory with 'cd'.
hacker@commands~an-epic-filesystem-quest:/$ cd /opt/busybox/busybox-1.33.2/include/config/unicode/neutral
hacker@commands~an-epic-filesystem-quest:/opt/busybox/busybox-1.33.2/include/config/unicode/neutral$ ls 
GIST  table.h
hacker@commands~an-epic-filesystem-quest:/opt/busybox/busybox-1.33.2/include/config/unicode/neutral$ cat GIST
Yahaha, you found me!
The next clue is in: /usr/local/lib/python3.8/dist-packages/angr/procedures/posix/__pycache__

The next clue is **hidden** --- its filename starts with a '.' character. You'll need to look for it using special options to 'ls'.
hacker@commands~an-epic-filesystem-quest:/opt/busybox/busybox-1.33.2/include/config/unicode/neutral$ cd /usr/local/lib/python3.8/dist-packages/angr/procedures/posix/__pycache__
hacker@commands~an-epic-filesystem-quest:/usr/local/lib/python3.8/dist-packages/angr/procedures/posix/__pycache__$ ls -a
.                        dup.cpython-38.pyc            htons.cpython-38.pyc      read.cpython-38.pyc        socket.cpython-38.pyc
..                       fcntl.cpython-38.pyc          inet_ntoa.cpython-38.pyc  readdir.cpython-38.pyc     strcasecmp.cpython-38.pyc
.SNIPPET                 fdopen.cpython-38.pyc         listen.cpython-38.pyc     recv.cpython-38.pyc        strdup.cpython-38.pyc
__init__.cpython-38.pyc  fileno.cpython-38.pyc         mmap.cpython-38.pyc       recvfrom.cpython-38.pyc    strtok_r.cpython-38.pyc
accept.cpython-38.pyc    fork.cpython-38.pyc           open.cpython-38.pyc       select.cpython-38.pyc      syslog.cpython-38.pyc
bind.cpython-38.pyc      getenv.cpython-38.pyc         opendir.cpython-38.pyc    send.cpython-38.pyc        tz.cpython-38.pyc
bzero.cpython-38.pyc     gethostbyname.cpython-38.pyc  poll.cpython-38.pyc       setsockopt.cpython-38.pyc  unlink.cpython-38.pyc
chroot.cpython-38.pyc    getpass.cpython-38.pyc        pread64.cpython-38.pyc    sigaction.cpython-38.pyc   usleep.cpython-38.pyc
close.cpython-38.pyc     getsockopt.cpython-38.pyc     pthread.cpython-38.pyc    sim_time.cpython-38.pyc    write.cpython-38.pyc
closedir.cpython-38.pyc  htonl.cpython-38.pyc          pwrite64.cpython-38.pyc   sleep.cpython-38.pyc
hacker@commands~an-epic-filesystem-quest:/usr/local/lib/python3.8/dist-packages/angr/procedures/posix/__pycache__$ cat .SNIPPET
Great sleuthing!
The next clue is in: /opt/linux/linux-5.4/drivers/video/fbdev/mb862xx
hacker@commands~an-epic-filesystem-quest:/usr/local/lib/python3.8/dist-packages/angr/procedures/posix/__pycache__$ cd /opt/linux/linux-5.4/drivers/video/fbdev/mb862xx
hacker@commands~an-epic-filesystem-quest:/opt/linux/linux-5.4/drivers/video/fbdev/mb862xx$ ls
MEMO  Makefile  mb862xx-i2c.c  mb862xx_reg.h  mb862xxfb.h  mb862xxfb_accel.c  mb862xxfb_accel.h  mb862xxfbdrv.c
hacker@commands~an-epic-filesystem-quest:/opt/linux/linux-5.4/drivers/video/fbdev/mb862xx$ cat MEMO
Lucky listing!
The next clue is in: /usr/lib/python3/dist-packages/ptyprocess/__pycache__
hacker@commands~an-epic-filesystem-quest:/opt/linux/linux-5.4/drivers/video/fbdev/mb862xx$ cd /usr/lib/python3/dist-packages/ptyprocess/__pycache__
hacker@commands~an-epic-filesystem-quest:/usr/lib/python3/dist-packages/ptyprocess/__pycache__$ ls
INFO  __init__.cpython-38.pyc  _fork_pty.cpython-38.pyc  ptyprocess.cpython-38.pyc  util.cpython-38.pyc
hacker@commands~an-epic-filesystem-quest:/usr/lib/python3/dist-packages/ptyprocess/__pycache__$ cat INFO
Great sleuthing!
The next clue is in: /opt/linux/linux-5.4/include/config/need/per/cpu/page
hacker@commands~an-epic-filesystem-quest:/usr/lib/python3/dist-packages/ptyprocess/__pycache__$ cd /opt/linux/linux-5.4/include/config/need/per/cp
u/page
hacker@commands~an-epic-filesystem-quest:/opt/linux/linux-5.4/include/config/need/per/cpu/page$ ls
README  first
hacker@commands~an-epic-filesystem-quest:/opt/linux/linux-5.4/include/config/need/per/cpu/page$ cat README
CONGRATULATIONS! Your perserverence has paid off, and you have found the flag!
It is: pwn.college{AeLXxRBhxnLiGlSiKhWAny47tQc.QX5IDO0wiN4AzNzEzW}

```
I used the instructions available and the hints being given to keep moving ahead with the challenge and finally find the flag. 

## What I learned
I learned the detailed use and practiced the usage of cd, ls and cat functions.

## References 
I used the resources available on pwn.college as my reference.
