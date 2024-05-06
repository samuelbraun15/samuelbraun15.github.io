---
layout: post
title: "'Logon' challenge"
date: 2024-03-26
categories: CTF
tags: [cyber-security, reverse-engineering, web-exploitation]
author: sam
img_path: /assets/img/posts
---

![Pic 1](logondesc.webp){:style="display:block; margin-left:auto; margin-right:auto; width:80%; border: 3px solid black;"}


The factory is hiding things from all of its users 

Let's login as Joe and find what they've been looking at in [Problem 13594](https://jupiter.challenges.picoctf.org/problem/13594/)

> In a city buzzing with rumors of hidden riches, a mysterious factory loomed, its secrets elusive.
Joe, an inquisitive soul, embarked on a quest to uncover the truth behind the factory's mystique. With determination and a keen eye, he delved into its digital depths.

> Step by step, Joe unraveled the layers of secrecy, uncovering clues to its clandestine activities. Through persistence and observation, he pieced together the puzzle, drawing closer to the heart of the mystery.
And finally, with a few keystrokes, Joe cracked the code, revealing the factory's hidden treasures to the world.
Emerging from his digital expedition, Joe felt a sense of accomplishment.

> Another mystery solved, another victory for curiosity. And amidst the city's hustle and bustle, Joe knew there were always more secrets waiting to be uncovered, more puzzles waiting to be solved.

## Option 1 - Burp Suite 


> Hint 1 : Hmm it doesn't seem to check anyone's password, except for Joe's?
{: .prompt-info }
 

- Log in as joe. Put password as anything ‘aaa’ for example.
- You log in but it says you will not be able to see the flag. 
- For this we want to check why its saying that, why is it only half way done? 
- Open up Burp Suite and turn on ‘intercept’ log in again as Joe with a password. 
- You wlll notice that in the cookies you see ‘admin=False’ 
![Pic 1](logon1.webp){:style="display:block; margin-left:auto; margin-right:auto; width:80%; border: 3px solid black;"}
_Login attempt via Burp Suite_

Lets change that to True, then hit forward. Your flag will be revealed.



#### The Burp Suite Expedition
- Interception and Analysis: Burp Suite's interception feature allows cybersecurity professionals to intercept and analyze HTTP requests and responses between a client (such as a web browser) and a server. This is invaluable for understanding how web applications function and identifying potential security vulnerabilities.
- Cookie Manipulation: By intercepting the login request and modifying the cookie value from "admin=False" to "admin=True," we demonstrate the critical importance of proper session management and access control. Insecure handling of session cookies, such as failing to enforce proper authentication checks on the server-side, can lead to privilege escalation attacks and unauthorized access to sensitive resources.
- Understanding Authentication Mechanisms: This option highlights the importance of understanding how authentication mechanisms work within web applications. In this case, the application incorrectly relies solely on the "admin" cookie to determine user privileges, rather than performing proper authentication checks based on username and password. This oversight exposes a critical security flaw that could be exploited by attackers.


## Option 2 - Curl Command 

- Log in as Joe so you get ‘ Success, not sure youll be able to see the flag though; 
- Right click go to inspect. 
- Go to Application
- Go to cookies 

![Pic 2](logon2.webp){:style="display:block; margin-left:auto; margin-right:auto; width:80%; border: 3px solid black;"}
_Login attempt via curl command_

- Find ‘ admin = False’ and right click ‘ Show Requests With This Cookie’ 
- Choose flag with the status 200 since 307 is a redirect. Right click and ‘Copy as cURL’ ( not all as cURL)
- Paste it into kali linux terminal and change the value of admin to True 

![Pic 3](logon3.webp){:style="display:block; margin-left:auto; margin-right:auto; width:80%; border: 3px solid black;"}
_Flipping admin from False to True_

![Pic 4](logon4.webp){:style="display:block; margin-left:auto; margin-right:auto; width:80%; border: 3px solid black;"}
_There's the flag!_

### The Curl Command Odyssey
- Manual HTTP Requests with Curl: Curl is a powerful command-line tool for making HTTP requests. This option demonstrates how cybersecurity professionals can use Curl to manually replicate and modify HTTP requests, providing greater control and flexibility for testing and analysis.
- Cookie-Based Authentication Bypass: By altering the "admin" cookie value to "True," we exploit a flaw in the application's authentication logic, effectively bypassing the intended access control mechanism. This highlights the importance of thorough testing and validation of input data, especially when it influences user privileges or access rights.
- Automating Exploitation with Scripts: While this option involves manual manipulation of Curl commands, the same principles can be applied to automate exploitation using scripting languages such as Python. Understanding how to automate tasks like cookie manipulation can significantly streamline the process of identifying and exploiting security vulnerabilities in web applications.

In conclusion, mastering tools like Burp Suite and Curl, along with understanding the underlying principles of web application security, is essential for cybersecurity professionals tasked with securing modern digital ecosystems against ever-evolving threats.
