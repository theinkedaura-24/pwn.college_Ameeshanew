# hidden files
This challenge asks us to view hidden files in ls by invoking ls with -a.

## My solve
**Flag:** `pwn.college{k24Bm0mloc_6xWIgyrr6UMRjN6x.QXwUDO0wiN4AzNzEzW}`


```hacker@commands~hidden-files:~$ ls -a /
.   .dockerenv            bin   challenge  etc   lib    lib64   media  nix  proc  run   srv  tmp  var
..  .flag-14664139157499  boot  dev        home  lib32  libx32  mnt    opt  root  sbin  sys  usr
hacker@commands~hidden-files:/$ cat /.flag-14664139157499
pwn.college{k24Bm0mloc_6xWIgyrr6UMRjN6x.QXwUDO0wiN4AzNzEzW}
```

## What I learned
I learned how to invoke ls with -a to show the hidden files in ls.

## References 
I used the resources available on pwn.college as my reference.
