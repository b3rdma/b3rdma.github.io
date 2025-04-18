---
layout: post
title: 'Understanding the Bad Horse Traceroute: A Curious Discovery in Networking'
date: '2025-04-18 18:29 +0100'
author: b3rdma
description: >-
  An interesting find and implementation of DNS/reverse DNS for expressing an
  artistic fun side with otherwise mundane but useful tools
image: null
categories:
  - Networking
  - DNS
tags:
  - traceroute
  - reverse dns
  - bad.horse
  - dr. horrible
  - routing
---
<!-- prettier-ignore-start -->
> This little gem was an assignment in the _SOC Analyst NOW_ course created by [CyberNOW](https://www.cybernoweducation.com/).
{: .prompt-info :}
<!-- prettier-ignore-end -->

## Contents

<!-- toc -->

- [What is Traceroute and How Does It Work?](#what-is-traceroute-and-how-does-it-work)
- [The Bad Horse Traceroute](#the-bad-horse-traceroute)
- [What is Dr. Horrible's Sing-Along Blog?](#what-is-dr-horribles-sing-along-blog)
- [How Does It Work? (Simplified)](#how-does-it-work-simplified)
  * [1. Registering a Domain and IP Range](#1-registering-a-domain-and-ip-range)
  * [2. Chaining Routers](#2-chaining-routers)
  * [3. Reverse DNS (PTR Records)](#3-reverse-dns-ptr-records)
  * [4. Engineering the Path](#4-engineering-the-path)
- [Try It Yourself](#try-it-yourself)
- [Creative Network Engineering](#creative-network-engineering)
- [Conclusion](#conclusion)
  * [References](#references)

<!-- tocstop -->

## What is Traceroute and How Does It Work?

As someone still finding their feet in networking after a military career, tools
like `traceroute` have become an unexpected source of fascination. At its core,
`traceroute` is a diagnostic tool used to map the path your data packets take to
reach a server. It sends packets with gradually increasing Time To Live (TTL)
values and tracks each router that responds along the way.

This produces a list of _hops_, each one a network device your data passes
through.

<!-- prettier-ignore-start -->
```bash
traceroute cybernoweducation.com
```
{: .nolineno }
<!-- prettier-ignore-end -->

Usually, this gives you a technical breakdown of network performance. But now
and then you come across something more playful, something that reminds you
there's creativity in the command line.

## The Bad Horse Traceroute

If you run:

<!-- prettier-ignore-start -->
```bash
traceroute bad.horse
```
{: .nolineno }
<!-- prettier-ignore-end -->

You're not just tracing a network path, you're following the lyrics of _Bad
Horse_, a song from _Dr. Horrible's Sing-Along Blog_. Each hop in the traceroute
is a hostname that forms part of the lyrics.

You might see something like:

<!-- prettier-ignore-start -->
```text
17 he.rides.across.the.nation (162.252.205.134)
18 the.thoroughbred.of.sin (162.252.205.135)
```
{: .nolineno }
<!-- prettier-ignore-end -->

The full song plays out hop by hop, like a singing telegram delivered one router
at a time.

## What is Dr. Horrible's Sing-Along Blog?

It's a musical web series created by Joss Whedon during the 2007–2008 writers'
strike. The story follows Dr. Horrible, an aspiring supervillain, and features
the mysterious _Bad Horse_, leader of the Evil League of Evil.

Bad Horse doesn't speak. He sends singing telegrams. And that's exactly what
this traceroute recreates.

## How Does It Work? (Simplified)

This was the brainchild of James Renken, who set it up in 2014 and it went live
in 2015. Here's how it works:

### 1. Registering a Domain and IP Range

He registered the domain `bad.horse` and acquired a block of consecutive IP
addresses (e.g., 162.252.205.130 through 162.252.205.157).

### 2. Chaining Routers

Each router (or virtual hop) was configured to forward traffic to the next. That
way, traceroute would follow the precise sequence he wanted.

### 3. Reverse DNS (PTR Records)

Each IP was given a hostname that corresponds to a line of the song. That's done
using reverse DNS, which maps IPs back to human-readable names.

```text
162.252.205.134 -> he.rides.across.the.nation
162.252.205.135 -> the.thoroughbred.of.sin
```

### 4. Engineering the Path

With PTR records and careful routing, a simple traceroute becomes a
lyric-by-lyric musical trail.

## Try It Yourself

Want to see it firsthand?

- On **Windows**:

<!-- prettier-ignore-start -->
```cmd
tracert bad.horse
```
{: .nolineno }
<!-- prettier-ignore-end -->

- On **macOS/Linux**:

<!-- prettier-ignore-start -->
```bash
traceroute bad.horse
```
{: .nolineno }
<!-- prettier-ignore-end -->

If you want to make sure you see the full song, you may need to increase the hop
limit:

- On **Windows**:

<!-- prettier-ignore-start -->
```bash
tracert -h 60 bad.horse
```
{: .nolineno }
<!-- prettier-ignore-end -->

- On **macOS**:

<!-- prettier-ignore-start -->
```bash
traceroute -m 60 bad.horse
```
{: .nolineno }
<!-- prettier-ignore-end -->

There are other more complex commands, using `cut`, `sed`, `tr`, `xargs`, and
`say` that can speak out the lyrics direct from the `traceroute` command. Love
it. One example that worked on my macOS set up is:

<!-- prettier-ignore-start -->
```bash
traceroute -m 64 bad.horse | sed "s/^....//g" | cut -d " " -f 1 | tr "." " " | xargs say -v
```
{: .nolineno }
<!-- prettier-ignore-end -->

## Creative Network Engineering

This whole project is a great example of how technical skill and imagination can
come together. It's not just about routing and DNS, it's about storytelling
through infrastructure.

Honestly, as someone new to this world, it was inspiring to come across this. It
made the concept of PTR records and routing loops stick in a way that a textbook
never could. Thanks to _Tyler Wall_ at [CyberNOW](https://www.cybernoweducation.com) for the assignment that showcased this
piece of art.

## Conclusion

The Bad Horse traceroute is clever, geeky, and joyful; it shows that even
network tools can carry a tune. If you're just getting into networking, don't be
surprised when you stumble across little gems like this.

Not everything on the command line has to be purely functional. Sometimes, it
sings.

---

### References

1. [The Verge](https://www.theverge.com/tldr/2015/9/25/9398889/dr-horrible-traceroute-bad-horse)
2. [Sean Breeden's Blog](https://www.seanbreeden.com/blog/dr-horribles-traceroute.html)
3. [Cloudflare: Reverse DNS](https://www.cloudflare.com/learning/dns/glossary/reverse-dns/)
4. [Stack Overflow - How It Works](https://stackoverflow.com/questions/32870553/telling-a-story-with-traceroute-how-does-it-work)
5. [A2WD Blog](https://www.a2wd.com/traceroute-bad-horse/)

