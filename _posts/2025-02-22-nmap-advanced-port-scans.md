---
layout: post
title: NMAP Advanced Port Scans
date: '2025-02-22 23:20 +0000'
author: b3rdma
description: My notes on NMAP from the THM room NMAP Advanced Port Scans
image: null
categories:
  - Tools
  - Network
tags:
  - nmap
  - enumeration
  - privilege escalation
---
<!-- prettier-ignore-start -->
> These are my notes from the THM NMAP Basic Port Scans room. No teaching in
> this post, just personal notes on basic NMAP usage.
{: .prompt-warning }
<!-- prettier-ignore-end -->

## Contents

### Table of contents

<!-- toc -->

- [TCP Null Scan, FIN Scan and XMAS Scan.](#tcp-null-scan-fin-scan-and-xmas-scan)
  * [Null Scan](#null-scan)
  * [FIN Scan](#fin-scan)
  * [XMAS Scan](#xmas-scan)
  * [TCP Maimon Scan](#tcp-maimon-scan)
  * [TCP ACK Scan](#tcp-ack-scan)
  * [Window Scan](#window-scan)
  * [Custom Scan](#custom-scan)
  * [Spoofing and Decoys](#spoofing-and-decoys)

<!-- tocstop -->

Some advanced scans are useful against certain specific systems, others will be
useful in particular network setups.

## TCP Null Scan, FIN Scan and XMAS Scan.

### Null Scan

- The Null Scan does not set any flag; all six flag bits are set to zero. This
  scan is chosen using the flag `nmap -sN` (Null Scan).
- No reply should be recieved, so a lack of reply in a null scan means _either_
  that the port is open or a firewall is blocking the packet.
- If the port is closed, a packet with RST bit set will be sent back to the
  host.
- A lack of a RST packet in the Null Scan indicates that the port is not closed:
  _*open or filtered*_.

<!-- prettier-ignore-start -->
```bash
sudo nmap -sN <Target IP>
```
{: .nolineno }
<!-- prettier-ignore-end -->

### FIN Scan

- The FIN Scan sets the FIN flag bit to 1. This scan is chosen using the flag
  `nmap -sF` (FIN Scan).
- Results are similar to the Null Scan. Some firewalls do not respond with a RST
  packet in the FIN scan.

<!-- prettier-ignore-start -->
```bash
sudo nmap -sF <Target IP>
```
{: .nolineno }
<!-- prettier-ignore-end -->

### XMAS Scan

- The XMAS Scan sets the FIN, PSH and URG flags to 1. This scan is chosen using
  the flag `nmap -sX` (XMAS Scan).
- Results are similar to the FIN Scan. Some firewalls do not respond with a RST
  packet in the XMAS scan.

<!-- prettier-ignore-start -->
```bash
sudo nmap -sX <Target IP>
```
{: .nolineno }
<!-- prettier-ignore-end -->

### TCP Maimon Scan

- In the Maimon scan, the FIN and ACK flags are set to 1. This scan is chosen
  using the flag `nmap -sM` (Maimon Scan).
- This scan will not work on most targets in modern networks. Definitely not the
  type to pick first but maybe useful in fringe cases.

<!-- prettier-ignore-start -->
```bash
sudo nmap -sM <Target IP>
```
{: .nolineno }
<!-- prettier-ignore-end -->

### TCP ACK Scan

- The ACK Scan sets the ACK flag bit to 1. This scan is chosen using the flag
  `nmap -sA` (Ack Scan).
- This scan is useful if there is a firewall in front of the target.
  Consequently, based on which ACK packets result in responses, we can learn which
  ports are not blocked by the firewall.
- This scan is more suitable to discover firewall rule sets and configuration.

<!-- prettier-ignore-start -->
```bash
sudo nmap -sA <Target IP>
```
{: .nolineno }
<!-- prettier-ignore-end -->

### Window Scan

- The TCP window scan is almost the same as the TCP ACK scan; however it
  examines the TCP Window field of the RST packet in the response.
- On specific systems, it can be used to discover if a port is open.

<!-- prettier-ignore-start -->
```bash
sudo nmap -sW <Target IP>
```
{: .nolineno }
<!-- prettier-ignore-end -->

### Custom Scan

- If we want to experiment with a new TCP flag combination beyond the built-in
  TCP scan types, we can use `--scanflags`.
- Example; to send SYN, RST and FIN simultaneously, use `--scanflags RSTSYNFIN`.

<!-- prettier-ignore-start -->
```bash
sudo nmap --scanflags RSTSYNFIN <Target IP>
```
{: .nolineno }
<!-- prettier-ignore-end -->

### Spoofing and Decoys

- Spoofing can be useful in some network setups. We will need to be able to
  monitor the network traffic as the return packets will go to the spoofed source
  IP address and not our IP address.
- Will need to specify the network interface using the `-e` flag, and disable
  ping scan with `-Pn`.

<!-- prettier-ignore-start -->
```bash
sudo nmap -e <Interface> -Pn -S <Spoofed IP> <Target IP>
```
{: .nolineno }
<!-- prettier-ignore-end -->

- If on the same subnet, can also spoof the MAC address using `--spoof-mac <MAC
address>`.
- Spoofing only works in a minimal number of cases. Decoys are more flexible and
  can be used in many network setups.

<!-- prettier-ignore-start -->
```bash
sudo nmap -D 10.10.0.1,10.10.0.2,RND,RND,ME <Target IP>
```
{: .nolineno }
<!-- prettier-ignore-end -->

- Other reading areas on scans include:
  - Fragmented Packets
  - Idle/Zombie Scan
  - Getting more details with `--reason`, `-v/-vv` or `-d/-dd`.

