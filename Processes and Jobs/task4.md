# Killing Misbehaving Processes
This challenge asks us to kill a "decoy" program that is preventing us from getting the flag. The decoy program is holding on to a special file called a named pipe at /tmp/flag_fifo. The main program, /challenge/run, needs to write the flag to this pipe, but it can't because the decoy is in the way.
To solve this, we need to:
Use the ps command to find the unique ID (PID) of the /challenge/decoy process.
Use the kill command with that PID to stop the decoy.
Once the decoy is gone, we can run /challenge/run to make it write the flag to the pipe and complete the challenge.

## My solve
**Flag:** `pwn.college{M7o83fc6pfdNj0FDx6cy67aI8bD.0FNzMDOxwiN4AzNzEzW}`
```
hacker@processes~killing-misbehaving-processes:~$ ps -ef | grep /challenge/decoy
root         139       1  0 19:03 ?        00:00:00 su -c exec /challenge/decoy > /tmp/flag_fifo hacker
hacker       142     139  0 19:03 ?        00:00:00 /usr/bin/python /challenge/decoy
hacker       197     176  0 19:04 pts/0    00:00:00 grep --color=auto /challenge/decoy
hacker@processes~killing-misbehaving-processes:~$ kill 142
hacker@processes~killing-misbehaving-processes:~$ cat /tmp/flag_fifo
pwn.college{SsQopm6ZD5vFs2ci-5Ac-YeTTTZwxnmmnRVNEsOeaNI8Mca}
pwn.college{CKTT6inEINYwQg7Y30RRX6FYT60tIhwzR5SGmQSjCHSO0S6}
pwn.college{pZ62hY3lOoQi.eBCRnUfu0vBgSYi871E2aw1Q6Xq.iRY6mY}
pwn.college{h7YXc8.-PIidNxm2a3G6Zt0yoi1NzDCth5lEvt8Dm7BzSvd}
pwn.college{.LmtX8vfA9xWytdpuQieQJBF0obXzJPJAlk5ATVxippP0tu}
pwn.college{89xRFiojz.jIcrhm-CyRyAwqSQMRlFON6yl2DbFEH70Id7M}
pwn.college{Dm3ztGAr3rATsGRyzw8akKsSbGUYbAREbvHwY1C4nPw3dXj}
pwn.college{gAjuFKPjLSPo0JUsX5tICOJw0FTa0JMAaULMk09NA1gfw5L}
pwn.college{RUZlvDJdNyNbVA6pHz70qHTa9C4MNlxz2AK8b7hBbMv6JFf}
pwn.college{IW19pZiZ3SEnhkykk3lNqJ.PoWxaeoh72GxJTlr0zIoV--f}
pwn.college{kx2IO764OIJ4X12jD9wznP6bs89KpveAQ9tgIX9XXeeiC1-}
pwn.college{pBMhiLf2Fg.mjgHuDOH5LKLM7spHtIvbxFh7SCPvDxWbLYa}
pwn.college{sFUXAttyPFZitMS1XCdMSeXFefMUrr7OxmV8kJZVnr5n.Li}
pwn.college{0uBzJfPbCWAd1oBkurKeD0w9Xi5lhqAt6r3O8FPI7Hhkz8w}
pwn.college{0XFw5G9JibBRxbSKXpyRkRqFOohHuqx-hcCD4IbNa1ERJdc}
pwn.college{52BZxHrtLo1G5.faP2rg4yscgbqB2Ui-QVDUa-xAszDf2BX}
pwn.college{9e65Ocs-WE5vsDSFjgQsVGFvK2xbiv3GffpWn4RsR7wCVHS}
pwn.college{npq-gF-KgoEBfXrRv3Ta.yK5oLm9FdfvGzHMlqwqVYtLDq3}
pwn.college{ABguBr5tRAH.cRi3VnkhH9RMIoOa5QHdwo4bpB-CvdPNsZF}
pwn.college{cl5wXrW7q9EqXVKdvZiySCZ5psGBbH87Kkk5er8.sjmVpjC}
pwn.college{0kwE1tPgLOegPnYjr.ucgNImK4ukKHqafj8gCPNY1mSwA9w}
pwn.college{V2ky6O7vq7SX3nLMapoAK44oh5p1RRnpr-T0rtUQE4LBlq3}
pwn.college{ozj64KWtiLFlEkap9BFW2k2Emjh2Rd4-46xnlk1FCg-H4Gs}
pwn.college{AltWa5tTHY-tB8p0QMIAlfc6eoRWiEgrJyQJrYVb1wVxC3B}
pwn.college{wL3m.3luS7MKWwPu4sQE40kACkarsYPGt4xhetktBJ1oItR}
pwn.college{OQBDQI1GyKXIllq3Bey2R89XFSuFCuJsqUbfWFa64u50i55}
pwn.college{zwBIXtI8i4eQjwUj5KeQO4HWwdQSLSzeSgr.O3ulJj1Qtqi}
pwn.college{9-HCe7EuefGG5AsLmfIXbdOddpifcTB.KMzX8p9-JNzvjSE}
pwn.college{Zka.DtAcnII3kM4YTVDKcPVcXjfEWuA4R.rQi.wr0d2Egpk}
pwn.college{cfNYN6wukLuseN5E0s6sF2vuM.U5Cn5kiH5vklng7EuiSBd}
pwn.college{rgq4YV4fKPH022tsWoLzPw844haJNU5yf3sSv5g8t7uMlNZ}
pwn.college{W.azGN9r2q0BIXqjB8RtNYbMycgKQ218qP1vdwdCFWO6g86}
pwn.college{pntlI7YgtW5DAhfwJxD6inEDmbc-lhLQkVG9fmp2VTTf92k}
pwn.college{m8a2WDdUSdKefUajElIwioFkK16n8kgzmQUPIXI-YgpO74a}
pwn.college{eLuILsGuQBzJPdnjOmEEUehpLO9X0Z4ilESESyGHKo628UW}
pwn.college{mCZiyLeub-BeeHKrU8QW03Cf7UFjUFVvGOgCNeiEAoY10VA}
pwn.college{HgfvXJVkzWqhatNXOjm2llILCSrYp9NfENLyn4trBanu-W0}
pwn.college{R-FEJmxBTJmEpiogbwIKdQpEjcird.twXHPkdVZw0QMVVQX}
pwn.college{1z5cjFwbI6p-UJ3usnuCrtHjILNN2gVdwnb3myDsbrskS7g}
pwn.college{nyB67-mNBdN5irfg4zKFRnWiGZd352.mzyLNwgYai-zZqWC}
pwn.college{o9JBkoMPxh99fci-nFe8M0kh-wloneVH0e499BBeS0w-6VF}
pwn.college{idNRB1hbu8X3Jolo.82aMJyjTFl2WqR1a-IYB1ONTEAVnSi}
pwn.college{UajUjBmEAwvUK7pBT-vDgOKEGdmDPtG9aYRdmHeaYokwO7Z}
pwn.college{0NWzQwqDNI.6cZhri8XgrP8gNaEoa4qGcyUMU5NqjVgvPoH}
pwn.college{JP0BKE9AJZjsf1EtMicf2W7hH.ZnvId4EsdamhNKUgoalxE}
pwn.college{3XwLKsv1MO2iwdMQ5CXPMy4JpIEHi7o4PYMDj3AaMgd4xQf}
pwn.college{GlvWTvRWj.-VumgAA5j-AmbFW.ISl5Hi8LXstq4uFOxwfQg}
pwn.college{JD2SjcgEorNarshMx33ZfK7A6rgFqq2C6Q8.Ihiy9pB-8eO}
/challenge/run
pwn.college{M7o83fc6pfdNj0FDx6cy67aI8bD.0FNzMDOxwiN4AzNzEzW}
```

## What I learned
I learned that processes can "hog" a resource, such as a named pipe (FIFO), and that I can use the kill command to terminate them. This is a crucial skill for troubleshooting and managing system resources when a misbehaving process is interfering with other programs.

The challenge required me to follow a specific workflow:

Identify the problem process: I used the ps command to list all running processes and found the PID of /challenge/decoy.

Terminate the process: I then used the kill command with that PID to stop the decoy program from hogging the /tmp/flag_fifo pipe.

Run the intended program: With the decoy gone, /challenge/run was able to successfully write the flag to the pipe, which I could then read.

This taught me a practical application of process management: using ps and kill in a sequence to free up a shared resource and allow other programs to function correctly.

## References 
I used the videos and documents available on pwn.college as my references.
