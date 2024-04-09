---
layout: post
title: "Buffer Overflow 1 Challenge - Part 2"
date: 2024-04-09
categories: CTF
tags: [cyber-security, reverse-engineering, binary-exploitation, python]
author: sam
img_path: /assets/img/posts
---

## Buffer Overflow 1 Challenge - Part 2 - PicoCTF

- Now we know the unknown address that effects the vuln. Lets find the offset

```bash
/usr/share/metasploit-framework/tools/exploit/pattern_offset.rb -q 0x35624134   
```
![Pic 5](buffer5.webp){:style="display:block; margin-left:auto; margin-right:auto; width:80%; border: 3px solid black;"}
_Offset found_

> [*] Exact match at offset 44
