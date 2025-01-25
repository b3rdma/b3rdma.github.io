---
title: NMAP Netowrk Mapper Basics
date: '2025-01-05 21:35:00 +0000'
author: b3rdma
categories:
  - Tools
  - Network
tags:
  - nmap
  - enumeration
  - privilege escalation
---
## Contents
### Table of contents

<!-- toc -->

- [NMAP Links](#nmap-links)
- [What is NMAP?](#what-is-nmap)
- [Host Discovery](#host-discovery)
  * [NMAP host discovery using ARP](#nmap-host-discovery-using-arp)
  * [NMAP host discovery using ICMP](#nmap-host-discovery-using-icmp)
  * [NMAP host discovery using TCP and UDP](#nmap-host-discovery-using-tcp-and-udp)
- [Summary of Commands](#summary-of-commands)

<!-- tocstop -->

> These are my notes from the THM NMAP Introduction room. No teaching in this
> post, just personal notes on basic NMAP usage.
{: .prompt-info }

## NMAP Links

- [NMAP NSE](https://nmap.org/book/nse-usage.html) - Nmap Scripting Engine
- [NMAP Firewall/IDS Evasion](https://nmap.org/book/man-bypass-firewalls-ids.html) - Flags used to bypass firewalls and IDS

## What is NMAP?

NMAP is used for mapping networks, identifying live hosts, and discovering running services. NMAP's scripting engine can further extend its functionality, from fingerprinting services to exploiting vulnerabilities.
- A NMAP scan usually goes through the following steps:

  1. Enumerate targets
  2. Discover live hosts
  3. Reverse-DNS lookup
  4. Scan ports
  5. Detect versions
  6. Detect OS
  7. Traceroute
  8. Scripts
  9. Write output

- NMAP leverages the following protocols to discover live hosts:

  1. ARP from Link Layer
  2. ICMP from Network Layer
  3. TCP from Transport Layer
  4. UDP from Transport Layer

## Host Discovery

There are various ways to discover online hosts. When no host discovery options are provided, NMAP uses the following default options:

  1. When a _privileged_ user tries to scan targets on a local network (Ethernet), NMAP will use ARP from Link Layer.
  2. When a _privileged_ user tries to scan targets outside the local network, NMAP will use ICMP echo requests, TCP ACK to port 80, TCP SYN to port 443, and ICMP timestamp request.
  3. When an _unprivileged_ user tries to scan targets outside the local network, NMAP resorts to a TCP 3-way handshake by sending SYN packets to port 80 and port 443.

### NMAP host discovery using ARP

```zsh
sudo nmap -PR -sn 10.10.210.6/24
```
{: .nolineno }

Check details of the flags; `-PR` means only scan with ARP, `-sn` means scan only on local network, no DNS lookup.
Use in conjunction with WireShark to capture ARP packet details. An alternative is `arp-scan` which is installed.

### NMAP host discovery using ICMP

```bash
sudo nmap -PE -sn 10.10.210.6/24
```
{: .nolineno }

This method, `-PE` means only scan with ICMP echo request, `-sn` means scan only on local network, no DNS lookup.
Downside is that many firewalls block ICMP. Consider using ICMP Timestamp or ICMP Address Mask instead. Timestamp requests use the NMAP flag `-PP` and address mask requests use the NMAP flag `-PM`.

### NMAP host discovery using TCP and UDP

```bash
sudo nmap -PS -sn 10.10.210.6/24
```

The NMAP `-PS` flag means only scan with TCP SYN packets, `-sn` means scan only on local network, no DNS lookup.
The NMAP `-PA` flag means only scan with TCP ACK packets, `-sn` means scan only on local network, no DNS lookup.
For both the `-PS` and `-PA` flags, ports can be specified as well. E.g., `-PA21`, `-PA21-25`, and `-PA80,443,8080` are all valid port definitions. If no port specified, port 80 will be scanned.

With UDP, a response is not expected; however, if the port is closed, we expect to get an ICMP port unreachable packet which inicates the target system is up and available.
Syntax is as follows:

```zsh
sudo nmap -PU -sn 10.10.210.6/24
```

Port specification is similar to TCP as shown above.

## Summary of Commands

Summary of command-line options covered above:

| Scan Type              | Example Command                              |
| :--------------------- | :------------------------------------------- |
| ARP Scan               | `sudo nmap -PR -sn <TARGET_IP>/24`            |
| ICMP Echo Scan         | `sudo nmap -PE -sn <TARGET_IP>/24`            |
| ICMP Timestamp Scan    | `sudo nmap -PP -sn <TARGET_IP>/24`            |
| ICMP Address Mask Scan | `sudo nmap -PM -sn <TARGET_IP>/24`            |
| TCP SYN Ping Scan      | `sudo nmap -PS 22,80,443 -sn <TARGET_IP>/30`  |
| TCP ACK Ping Scan      | `sudo nmap -PA 22,80,443 -sn <TARGET_IP>/30`  |
| UDP Ping Scan          | `sudo nmap -PU 53,161,162 -sn <TARGET_IP>/30` |

Summary of other flags used:

- `-n` => No DNS lookup
- `-R` => Reverse-DNS lookup for all hosts
- `-sn` => host discovery only






