---
layout: post
title: "Buffer Overflow 1 Challenge - Part 1"
date: 2024-04-08
categories: CTF
tags: [cyber-security, reverse-engineering, binary-exploitation, python]
author: sam
img_path: /assets/img/posts
---

Imagine you have a storage locker with limited space, where you can only fit a certain number of items. 

- This locker represents a computer's memory buffer, which is used to temporarily store data while a program is running.

Now, let's say someone tries to stuff too many items into the locker. 

- This is like a buffer overflow in computer terms. A buffer overflow occurs when a program receives more data than it can handle, causing the excess data to overwrite adjacent memory locations.

When too many items are stuffed into the locker, some of them spill out onto the floor. 

- This is similar to how a buffer overflow can overwrite neighboring memory locations, potentially causing the program to crash or behave unpredictably.

Now, let's introduce the Python exploit. Imagine there's a clever trickster who realizes they can exploit this situation to their advantage. They carefully craft a payload of items designed to overflow the locker and spill out onto the floor in a specific way.

In computer terms, this payload is like a piece of code carefully constructed to exploit a buffer overflow vulnerability in a program. The payload is designed to overwrite the memory in a way that gives the trickster unauthorized access or control over the program's behavior.


## Buffer Overflow 1 Challenge - Part 1 - PicoCTF 


> Control the return address

> Now we're cooking! You can overflow the buffer and return to the flag function in the program.
You can view source here. And connect with it using 

> nc saturn.picoctf.net \<your-port-number-here\>

> Hint 1 : Make sure you consider big Endian vs small Endian.
{: .prompt-tip }

> Hint 2 : Changing the address of the return pointer can call different functions.
{: .prompt-tip }

1. Download the files 
    ![Pic 1](buffer1.webp){:style="display:block; margin-left:auto; margin-right:auto; width:80%; border: 3px solid black;"}
    _Downloads directory in Kali Linux_

2. Review the code
    ```bash
    void vuln(){
      char buf[BUFSIZE];
      gets(buf);
      // Further code...        
    ```

    - Think of the gets() function like a mouth that can eat whatever you give it.
    - In the code, gets() is used to eat whatever the user types on the keyboard and puts it into a box called buf.
    - However, gets() doesn't check if the box (buf) is too small for what you're feeding it. If you try to feed it too much, it will spill over and make a mess.
    - This "mess" is what we call a buffer overflow. It's like trying to put too many groceries in a bag, and they spill out onto the floor.

    - Buffer Size Declaration:
    : In the code, there's a line that says #define BUFSIZE 32. This means the box (buf) can only hold 32 characters. But the program won't check if what you're typing is more than 32 characters long. If you type more, it will overflow and make a mess.
    : So, even though the program only expects a small amount of input, if you give it more, it will cause trouble.
    No Stack Protection:
    : Just like how a castle might have guards to protect it from invaders, programs can have protection mechanisms to stop hackers.

    - In this case, the program doesn't have those guards. There's a compiler flag -fno-stack-protector used when compiling the program that tells it not to have those guards.
    - Without these guards, it's easier for a hacker to sneak in and cause trouble.
    - Potential Control Flow Manipulation:
    : Imagine you're driving a car and suddenly someone else grabs the steering wheel and decides where to go.
    : In the code, there's a part that prints out where the program is going next (get_return_address()). This is like telling the driver where to go next.
    : But if a hacker can put their own instructions in there, they can make the program go wherever they want, like telling the driver to go off a cliff!
    : This is dangerous because it lets a hacker take control of the program and make it do bad things.


    The code has some vulnerabilities that allow someone to give it too much input, bypass its protections, and even take control of where it goes next. This is a serious problem in cybersecurity because it can be exploited by hackers to do harmful things to the program or the system it's running on.

3. Create pattern to trigger the offset using metasploit 
    : Think of a special sequence of items that you can easily recognize. This is like creating a secret code. We'll put this code in the box and see where it spills out.

    ![Pic 2](buffer2.webp){:style="display:block; margin-left:auto; margin-right:auto; width:80%; border: 3px solid black;"}
    _Kali Linux terminal command for a 100 character sequence_

4. Using GDB to analyze the attack 

    Using GDB (GNU Debugger) can be very helpful for analyzing the behavior of the vulnerable program and understanding the impact of the buffer overflow. Here's how you can use GDB to debug the vuln program and determine the offset:

    ![Pic 3](buffer3.webp){:style="display:block; margin-left:auto; margin-right:auto; width:80%; border: 3px solid black;"}
    _This is how to gain permission_

    It seems that GDB encountered a permission denied error when attempting to execute the vuln program. This error typically occurs when the executable file does not have the necessary permissions to be executed.

    ```bash
    chmod +x /home/kali/Downloads/vuln
    ```

5. Launching the attack and the response 
    Jumping to Address: After entering the input, the program prints a message indicating that it's "Jumping to" a specific memory address (0x35624134). This address corresponds to the value of the instruction pointer (EIP) at the time of the crash.

    ![Pic 4](buffer4.webp){:style="display:block; margin-left:auto; margin-right:auto; width:80%; border: 3px solid black;"}
    _Segmentation Fault triggered_

    - Segmentation Fault: Suddenly, the program crashes with a "Segmentation fault" error. This means that the program tried to access memory it wasn't allowed to access. In simpler terms, it's like trying to reach for something on a shelf that's not there, causing you to stumble and fall.
    - Unknown Address: The crash occurs at an address (0x35624134) that doesn't correspond to any known function or instruction in the program. This indicates that the program's control flow has been hijacked, likely due to a buffer overflow or other vulnerability.

## CONT. NEXT POST