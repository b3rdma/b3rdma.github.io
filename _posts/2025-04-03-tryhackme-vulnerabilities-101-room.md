---
layout: post
title: TryHackMe Vulnerabilities 101 Room
date: '2025-04-03 15:04 +0100'
author: b3rdma
description: My notes on the THM room Vulnerabilities 101
image: null
categories:
  - Vulnerabilities
  - Exploits
tags:
  - cvss
  - cve
  - vpr
  - exploit-db
---
## Contents

<!-- toc -->

- [Common Vulnerability Scoring System (CVSS)](#common-vulnerability-scoring-system-cvss)
- [Vulnerability Priority Rating (VPR)](#vulnerability-priority-rating-vpr)
- [Vulnerability Databases](#vulnerability-databases)
- [Example Process](#example-process)

<!-- tocstop -->

## Common Vulnerability Scoring System (CVSS)

- Popular framework for scoring vulnerabilities.

- Advantages:

  1. CVSS has been around for a long time.
  2. Popular with organisations.
  3. Free and open source framework to adopt and recommended by organisations such as NIST (National Institute of Standards and Technology).

- Disadvantages:

  1. Never designed to help prioritise vulnerabilities.
  2. Heavily assesses vulnerabilities on an exploit being available.
  3. Vulnerabilities rarely change scoring after assessment despite the fact that new developments such as exploits may be found.

## Vulnerability Priority Rating (VPR)

- Much more modern framework developed by Tenable.
- The framework is considered to be risk-driven.

- Advantages:

  1. More modern framework that is real-world.
  2. Considers over 150 factors when calculating scores.
  3. Risk-driven and used by organisations to help prioritise patching vulnerabilities.
  4. Scorings are dynamic.

- Disadvantages:

  1. Not open-source like some other frameworks.
  2. Can only be adopted as part of a commercial platform.
  3. Does not consider the CIA triad to the extent that CVSS does.

## Vulnerability Databases

- [National Vulnerability Database (NVD)](https://nvd.nist.gov/vuln)

  - Lists all publicly categorised vulnerabilities.

- _CVE_ - Common Vulnerability and Exposures; have the formatting of `CVE-YEAR-IDNUMBER`, e.g., the famous WannaCry malware is listed as CVE-2017-0144.

  - Not a great website when searching for vulnerabilities for a specific application or scenario.

- [Exploit-DB](https://www.exploit-db.com) - by OffSec

  - Much more useful for hackers as it retains exploits for software and applications stored under the name, author, and version of the software or application.
  - Can also use to look for snippets of code (PoCs) that are used to exploit a specific vulnerability.

- Terms to know:

1. **_Vulnerability_** - A vulnerability is defined as a weakness or flaw in the design, implementation, or behaviours of a system or application.
2. **_Exploit_** - An exploit is something such as an action or behaviour that utilises a vulnerability on a system or application.
3. **_Proof of Concept (PoC)_** - A PoC is a technique or tool that often demonstrates the exploitation of a vulnerability.

## Example Process

1. Information Gathering - find out details of the target using OSINT; details such as number of clients, services offered, etc., possible software to attack such as a help desk or support application.
2. Enumeration and Scanning - Check for hosts, ports, and services that are open using NMAP.
3. Application Testing - Look for version numbers, try login credentials, try to access services, etc.
4. Check vulnerability databases for any exploits on the target software/services.
5. Use the exploit on the web application (or other service) to attempt to exploit the vulnerability.
6. Document all steps and details of the attack. Produce report and demonstrate PoC.

