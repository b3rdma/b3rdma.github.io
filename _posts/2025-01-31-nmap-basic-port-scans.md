---
id: nmap_basic_port
aliases: []
tags:
  - nmap
  - enumeration
  - privilege escalation
author: b3rdma
categories:
  - Tools
  - Network
date: '2025-01-31 16:35:00 +0000'
description: My notes on NMAP from the THM room NMAP Basic Port Scans
title: NMAP Basic Port Scans
---
> These are my notes from the THM NMAP Basic Port Scans room. No teaching in this post, just personal notes on basic NMAP usage.
{: .prompt-warning }

## Contents
### Table of contents

<!-- toc -->

- [TCP Flags](#tcp-flags)
- [TCP Connect Scan](#tcp-connect-scan)
- [TCP SYN Scan](#tcp-syn-scan)
- [UDP Scan](#udp-scan)
- [Fine-Tuning Scope and Performance](#fine-tuning-scope-and-performance)
  * [Port Selection](#port-selection)
  * [Performance (Scan Speed)](#performance-scan-speed)
- [Summary](#summary)

<!-- tocstop -->

## TCP Flags

NMAP can set or unset the following TCP flags within the TCP header:
  - URG: Urgent - indicates that the incoming data is urgent. With the URG flag
    is set, it is processed immediately without consideration of 
    having to wait on previously sent TCP segments.
  - ACK: Acknowledgment - Ack flag is used to acknowledge the receipt of a TCP segment.
  - PSH: Push function - Asking TCP to pass the data to the application layer promptly.
  - RST: Reset connection - Another device, such as a firewall, may have reset the connection. Also used when data is sent to a host and there is no service on the receiving end to answer. 
  - SYN: Synchronize sequence numbers - Used to initiate a TCP 3-way handshake and synchronize the sequence numbers with the other host.
  The sequence number should be set randomly during TCP connection establishment.
  - FIN: Finish data transfer - The sender has no more data to send.

## TCP Connect Scan

- TCP connect scan works by completing the TCP 3-way handshake.
- We are interested in learning if the TCP port is open, not in establishing a connection.
- The connection is torn as soon as its state is confirmed by sending RST/ACK.
- This is done using the `-sT` flag.

``` bash
nmap -sT <Target IP>
```
{: .nolineno }

## TCP SYN Scan

- Needs *privileged* user to run, i.e., `sudo`.
- The connection is torn down once a response is received from the server.
- Because a TCP connection was not established, it decreases the chances of the scan being logged.
- This is done using the `-sS` flag.

``` bash
sudo nmap -sS <Target IP>
```
{: .nolineno }

## UDP Scan

- Connectionless protocol and therefore no guarantee that a service listening on a UDP port would respond to packets.
- If a UDP packet is sent to a closed port, an ICMP port unreachable error (type 3, code 3) is returned.
- This is done using the `-sU` flag.

``` bash
sudo nmap -sU <Target IP>
```
{: .nolineno }

- UDP port scans take longer than TCP port scans.
- Add the `-F` flag to speed up the scan by only checking the 100 most common ports instead of the 1000 most common ports.
- Adding the `-v` flag to show the progress of the scan (verbose).

## Fine-Tuning Scope and Performance

### Port Selection

You can specify the ports you want to scan instead of the default 1000 ports. Specifying the ports is intuitive by now. Let’s see some examples:
- port list: `-p22,80,443` will scan ports 22, 80 and 443.
- port range: `-p1-1023` will scan all ports between 1 and 1023 inclusive, while `-p20-25` will scan ports between 20 and 25 inclusive.
- You can request the scan of all ports by using `-p-`, which will scan all 65535 ports.
- If you want to scan the most common 100 ports, add `-F`. Using `--top-ports <num>` will check the `<num>` most common ports.

### Performance (Scan Speed)

You can control the scan timing using `-T<0-5>`. `-T0` is the slowest (paranoid), while `-T5` is the fastest. According to NMAP manual page, there are six templates:

| Level | Name | Flag |
|:-:|-|:-:|
| 0 | paranoid | `-T0` |
| 1 | sneaky | `-T1` |
| 2 | polite | `-T2` |
| 3 | normal | `-T3` |
| 4 | aggressive | `-T4` |
| 5 | insane | `-T5` |

> To avoid IDS alerts, you might consider `-T0` or `-T1`. For instance, `-T0` scans one port at a time and waits 5 minutes between sending each probe,
so you can guess how long scanning one target would take to finish. If you don’t specify any timing, NMAP uses normal `-T3`. Note that `-T5` is the most aggressive in terms of speed;
however, this can affect the accuracy of the scan results due to the increased likelihood of packet loss. Note that `-T4` is often used during CTFs and when learning to scan on practice targets,
whereas `-T1` is often used during real engagements where stealth is more important.
{: .prompt-tip }

## Summary

|Port Scan Type    | Example Command             |
|------------------|-----------------------------|
|TCP Connect Scan  | `nmap -sT 10.10.247.147`      |
|TCP SYN Scan      | `sudo nmap -sS 10.10.247.147` |
|UDP Scan          | `sudo nmap -sU 10.10.247.147` |

|Option                | Purpose                         |
|----------------------|---------------------------------|
|`-p-`                   | all ports                       |
|`-p1-1023`              | scan ports 1 to 1023            |
|`-F`                    | 100 most common ports           |
|`-r`                    | scan ports in consecutive order |
|`-T<0-5>`               | slowest (T0) to fastest (T5)    |
|`--max-rate 50`         | rate `<=` 50 packets/sec          |
|`--min-rate 15`         | rate >= 15 packets/sec          |
|`--min-parallelism 100` | at least 100 probes in parallel |

> It is not best practice to add _new material_ to the summary that has not been covered in the body; however, I added a couple of flags to the second table as I want to have them there as a reminder but did not feel they needed a section of their own in my notes. I shall refer to the NMAP docs if I ever need more details. 
{: .prompt-info }








