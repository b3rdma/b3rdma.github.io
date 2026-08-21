---
title: Nmap Network Mapper Basics
date: "2025-01-05 21:35:00 +0000"
author: b3rdma
categories:
  - Tools
  - Network
tags:
  - nmap
  - enumeration
  - privilege escalation
description: My notes on Nmap from the THM room Nmap Basics
---

> These are my notes from the THM Nmap Introduction room. No teaching in this
> post, just personal notes on basic Nmap usage.
> {: .prompt-warning }

## Nmap Links

- [Nmap NSE](https://nmap.org/book/nse-usage.html) - Nmap Scripting Engine
- [Nmap Firewall/IDS Evasion](https://nmap.org/book/man-bypass-firewalls-ids.html) - Flags used to bypass firewalls and IDS

## What is Nmap?

Nmap is used for mapping networks, identifying live hosts, and discovering
running services. Nmaps scripting engine can further extend its functionality,
from fingerprinting services to exploiting vulnerabilities.

- A Nmap scan usually goes through the following steps:

  1. Enumerate targets
  2. Discover live hosts
  3. Reverse-DNS lookup
  4. Scan ports
  5. Detect versions
  6. Detect OS
  7. Traceroute
  8. Scripts
  9. Write output

- Nmap leverages the following protocols to discover live hosts:

  1. ARP from Link Layer
  2. ICMP from Network Layer
  3. TCP from Transport Layer
  4. UDP from Transport Layer

## Host Discovery

There are various ways to discover online hosts. When no host discovery options
are provided, Nmap uses the following default options:

1. When a _privileged_ user tries to scan targets on a local network (Ethernet),
   Nmap will use ARP from Link Layer.
2. When a _privileged_ user tries to scan targets outside the local network,
   Nmap will use ICMP echo requests, TCP ACK to port 80, TCP SYN to port 443,
   and ICMP timestamp request.
3. When an _unprivileged_ user tries to scan targets outside the local network,
   Nmap resorts to a TCP 3-way handshake by sending SYN packets to port 80 and
   port 443.

### Nmap host discovery using ARP

<!-- prettier-ignore-start -->
```zsh
sudo nmap -PR -sn 10.10.210.6/24
```
{: .nolineno }
<!-- prettier-ignore-end -->

Check details of the flags; `-PR` means only scan with ARP, `-sn` means scan
only on local network, no DNS lookup. Use in conjunction with WireShark to
capture ARP packet details. An alternative is `arp-scan` which is installed.

### Nmap host discovery using ICMP

<!-- prettier-ignore-start -->
```bash
sudo nmap -PE -sn 10.10.210.6/24
```
{: .nolineno }
<!-- prettier-ignore-end -->

This method, `-PE` means only scan with ICMP echo request, `-sn` means scan only
on local network, no DNS lookup. Downside is that many firewalls block ICMP.
Consider using ICMP Timestamp or ICMP Address Mask instead. Timestamp requests
use the Nmap flag `-PP` and address mask requests use the Nmap flag `-PM`.

### Nmap host discovery using TCP and UDP

<!-- prettier-ignore-start -->
```bash
sudo nmap -PS -sn 10.10.210.6/24
```
{: .nolineno }
<!-- prettier-ignore-end -->

`-PS<ports>` specifies TCP SYN probes for host discovery, while `-PA<ports>`
specifies TCP ACK probes. Ports may be given as individual ports, ranges, or
comma-separated lists, such as `-PA21`, `-PA21-25`, or `-PA80,443,8080`. `-sn` disables
port scanning after host discovery; it is not restricted to local networks. Use
`-n` separately to disable reverse-DNS lookups. If no port list is supplied, Nmap
uses its default discovery ports; typically TCP 443 for `-PS` and TCP 80 for `-PA`

With UDP, a response is not expected; however, if the port is closed, we expect
to get an ICMP port unreachable packet which indicates the target system is up
and available. Syntax is as follows:

<!-- prettier-ignore-start -->
```bash
sudo nmap -PU -sn 10.10.210.6/24
```
{: .nolineno }
<!-- prettier-ignore-end -->

Port specification is similar to TCP as shown above.

## Summary of Commands

Summary of command-line options covered above:

| Scan Type              | Example Command                               |
| :--------------------- | :-------------------------------------------- |
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
