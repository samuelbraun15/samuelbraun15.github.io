---
layout: post
title: "It is my birthday"
date: 2024-03-17
categories: CTF
tags: [cyber-security, reverse-engineering, web-exploitation]
author: Sam
img_path: /assets/img/posts
---

> Hint 1: Look at the category of this problem.
{: .prompt-tip }

> Hint 2: How may a PHP site check the rules in the description?
{: .prompt-tip }



>John decided to blend their birthday celebration with a lesson in cybersecurity for their friends. They crafted pairs of digital invitations that were nearly identical yet slightly different, laying the groundwork for an intriguing puzzle.

>The concept hinged on understanding the MD5 hash, best explained through the analogy of a "special factory machine." Imagine a factory where every item, regardless of its nature, is processed through this machine. Its task is to produce a distinct stamp for each item, akin to a digital fingerprint, designed to uniquely identify that item.

>However, the twist in John’s invitations highlighted a notable glitch in this machine—the phenomenon of two completely different items receiving the exact same stamp, a scenario known as a "collision." This was the essence of the challenge set by John.

>To participate, guests were instructed to upload their invitation pairs to a PHP website Alex had created. This site, functioning as the digital equivalent of a gatekeeper, employed a script to check if the two files, despite their differences, shared the same MD5 stamp. This process transformed a simple task into a live demonstration on the importance of digital authenticity and the inherent vulnerabilities in cryptographic systems.

>Through this simple yet effective demonstration, John introduced their friends to the concept of hash collisions, utilizing the factory machine analogy to demystify a complex principle of cybersecurity. The birthday became not just a gathering for celebration but a memorable and educational experience on the significance of digital security, showcasing John’s innovative approach to blending fun with learning.


![Pic 1](itbirthday1.webp){:style="display:block; margin-left:auto; margin-right:auto; width:80%; border: 3px solid black;"}

As you can see we have two uploadable links for PDF. Both require the same MD5 hash in order to reveal the flag. 

For this example im going to open up Kali Linux and go to the website 

https://www.mscs.dal.ca/~selinger/md5collision/

Here we can see 2 downloadable PDFs named "hello" and "erase" 

Once these are downloaded you can use these Kali Linux commands to check if they are similar. 

```bash
cd ~/Downloads
 
md5sum hello erase        

da5c61e1edc0f18337e46418e48c1290  hello
da5c61e1edc0f18337e46418e48c1290  erase

```

As you can see both files have the exact same MD5 hash but are different files completely. Lets go ahead and rename the files using the 'mv' command and then upload it to the website 

```bash
mv hello hello.pdf 
mv erase erase.pdf         
```

![Pic 2](itbirthday2.webp){:style="display:block; margin-left:auto; margin-right:auto; width:80%; border: 3px solid black;"}

Once uploaded you will be brought to this page 

```php
<?php

if (isset($_POST["submit"])) {
    $type1 = $_FILES["file1"]["type"];
    $type2 = $_FILES["file2"]["type"];
    $size1 = $_FILES["file1"]["size"];
    $size2 = $_FILES["file2"]["size"];
    $SIZE_LIMIT = 18 * 1024;

    if (($size1 < $SIZE_LIMIT) && ($size2 < $SIZE_LIMIT)) {
        if (($type1 == "application/pdf") && ($type2 == "application/pdf")) {
            $contents1 = file_get_contents($_FILES["file1"]["tmp_name"]);
            $contents2 = file_get_contents($_FILES["file2"]["tmp_name"]);

            if ($contents1 != $contents2) {
                if (md5_file($_FILES["file1"]["tmp_name"]) == md5_file($_FILES["file2"]["tmp_name"])) {
                    highlight_file("index.php");
                    die();
                } else {
                    echo "MD5 hashes do not match!";
                    die();
                }
            } else {
                echo "Files are not different!";
                die();
            }
        } else {
            echo "Not a PDF!";
            die();
        }
    } else {
        echo "File too large!";
        die();
    }
}
// FLAG: picoCTF{c0ngr4ts_u_r_1nv1t3d_aad886b9}

