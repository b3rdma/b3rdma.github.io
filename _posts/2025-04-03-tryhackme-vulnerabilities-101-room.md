---
layout: post
title: TryHackMe Vulnerabilities 101 Room
date: "2025-04-03 15:04 +0100"
author: b3rdma
description: My notes on the THM room Vulnerabilities 101
image: null
categories:
  - Hacking
  - Try Hack Me
tags:
  - vulnerabilities
  - exploits
  - cvss
  - cve
  - vpr
  - exploit-db
---

## Common Vulnerability Scoring System (CVSS)

- A widely used, open framework for communicating the technical characteristics and severity of vulnerabilities.
- CVSS scores range from 0 to 10, with higher scores representing greater severity.
- CVSS should not be treated as a complete vulnerability-prioritisation system. A high technical severity does not necessarily mean that a vulnerability presents the greatest risk to a particular organisation.

- Advantages:

  1. Well-established and widely adopted.
  2. Vendor-neutral and openly documented.
  3. Provides a consistent method of describing and comparing vulnerability severity.
  4. Can incorporate threat and environmental information in addition to the Base metrics.

- Limitations:

  1. The Base score represents technical severity rather than remediation priority.
  2. A Base score does not account for the importance of the affected asset within a particular organisation.
  3. Base characteristics are intentionally relatively static. Threat information, such as exploit maturity or active exploitation, needs to be considered separately through the relevant CVSS metrics or other vulnerability-prioritisation information.
  4. Using the Base score alone can therefore give an incomplete picture of real-world risk.

## Vulnerability Priority Rating (VPR)

- A proprietary vulnerability-prioritisation system developed by Tenable.
- VPR produces a dynamic score from 0.1 to 10 based on technical impact and current threat intelligence.
- Unlike a static severity score, VPR can change as the threat landscape develops.

- Advantages:

  1. Designed specifically to help prioritise vulnerability remediation.
  2. Uses threat intelligence and predictive analysis to consider the likelihood of exploitation.
  3. Considers factors such as vulnerability age, exploit maturity, threat activity and technical impact.
  4. Scores are dynamic and can change as new information becomes available.

- Limitations:

  1. Proprietary to Tenable rather than an open framework such as CVSS.
  2. Requires the Tenable ecosystem to make practical use of the scoring system.
  3. VPR assesses the vulnerability itself and does not, by itself, represent the complete risk to a particular organisation. Asset criticality and business context still need to be considered.

## Vulnerability Databases

- [National Vulnerability Database (NVD)](https://nvd.nist.gov/vuln)

  - Maintained by NIST.
  - Uses CVE records as its foundation and enriches them with vulnerability-management information such as severity metrics, affected products and references.

- _CVE_ - Common Vulnerabilities and Exposures.

  - Provides standard identifiers for publicly disclosed vulnerabilities.
  - CVE identifiers use the format `CVE-YEAR-IDNUMBER`.
  - For example, `CVE-2017-0144` identifies an SMB vulnerability that was exploited by WannaCry.
  - CVE identifiers make it possible for different organisations and security tools to refer unambiguously to the same vulnerability.

- [Exploit-DB](https://www.exploit-db.com) - maintained by OffSec.

  - Useful when researching publicly available exploits associated with particular software, applications and versions.
  - May contain proof-of-concept (PoC) exploit code demonstrating how a vulnerability can be exploited.

- Terms to know:

1. **_Vulnerability_** - A weakness or flaw in the design, implementation or behaviour of a system or application that may be exploited to cause an adverse effect.
2. **_Exploit_** - Code, a technique or another method that takes advantage of a vulnerability to produce an unintended result.
3. **_Proof of Concept (PoC)_** - A demonstration showing that a vulnerability can be exploited, without necessarily providing a complete or weaponised exploit.

## Example Process

The following represents a simplified vulnerability-assessment process within an authorised testing environment:

1. **Information Gathering** - Gather information about the target using OSINT, including exposed systems, services and potentially relevant applications.
2. **Enumeration and Scanning** - Identify hosts, ports and services using tools such as Nmap.
3. **Application Testing** - Identify software and version information and examine the exposed functionality and services.
4. **Vulnerability Research** - Search sources such as the NVD, CVE records and Exploit-DB for vulnerabilities affecting the identified software and versions.
5. **Validation** - Where authorised and appropriate, safely test whether a vulnerability can actually be exploited.
6. **Documentation** - Record the methodology, evidence and results, and produce a report or PoC where required.
