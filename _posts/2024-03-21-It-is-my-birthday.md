---
layout: post
title: "It is my birthday"
date: 2024-03-17
categories: CTF
tags: [cyber-security, reverse-engineering, web-exploitation]
author: Sam
img_path: assets/img/posts
---

> Hint 1: Look at the category of this problem.
{: .prompt-tip }

> Hint 2: How may a PHP site check the rules in the description?
{: .prompt-tip }



John decided to blend their birthday celebration with a lesson in cybersecurity for their friends. They crafted pairs of digital invitations that were nearly identical yet slightly different, laying the groundwork for an intriguing puzzle.

The concept hinged on understanding the MD5 hash, best explained through the analogy of a "special factory machine." Imagine a factory where every item, regardless of its nature, is processed through this machine. Its task is to produce a distinct stamp for each item, akin to a digital fingerprint, designed to uniquely identify that item.

However, the twist in John’s invitations highlighted a notable glitch in this machine—the phenomenon of two completely different items receiving the exact same stamp, a scenario known as a "collision." This was the essence of the challenge set by John.

To participate, guests were instructed to upload their invitation pairs to a PHP website Alex had created. This site, functioning as the digital equivalent of a gatekeeper, employed a script to check if the two files, despite their differences, shared the same MD5 stamp. This process transformed a simple task into a live demonstration on the importance of digital authenticity and the inherent vulnerabilities in cryptographic systems.

Through this simple yet effective demonstration, John introduced their friends to the concept of hash collisions, utilizing the factory machine analogy to demystify a complex principle of cybersecurity. The birthday became not just a gathering for celebration but a memorable and educational experience on the significance of digital security, showcasing John’s innovative approach to blending fun with learning.


!itsmybirthdaypic.jpeg

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

Once uploaded you will be brought to this page 

---html
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

?>
<!DOCTYPE html>
<html lang="en">

<head>
    <title>It is my Birthday</title>


    <link href="https://maxcdn.bootstrapcdn.com/bootstrap/3.2.0/css/bootstrap.min.css" rel="stylesheet">

    <link href="https://getbootstrap.com/docs/3.3/examples/jumbotron-narrow/jumbotron-narrow.css" rel="stylesheet">

    <script src="https://ajax.googleapis.com/ajax/libs/jquery/3.3.1/jquery.min.js"></script>

    <script src="https://maxcdn.bootstrapcdn.com/bootstrap/3.3.7/js/bootstrap.min.js"></script>


</head>

<body>

    <div class="container">
        <div class="header">
            <h3 class="text-muted">It is my Birthday</h3>
        </div>
        <div class="jumbotron">
            <p class="lead"></p>
            <div class="row">
                <div class="col-xs-12 col-sm-12 col-md-12">
                    <h3>See if you are invited to my party!</h3>
                </div>
            </div>
            <br/>
            <div class="upload-form">
                <form role="form" action="/index.php" method="post" enctype="multipart/form-data">
                <div class="row">
                    <div class="form-group">
                        <input type="file" name="file1" id="file1" class="form-control input-lg">
                        <input type="file" name="file2" id="file2" class="form-control input-lg">
                    </div>
                </div>
                <div class="row">
                    <div class="col-xs-12 col-sm-12 col-md-12">
                        <input type="submit" class="btn btn-lg btn-success btn-block" name="submit" value="Upload">
                    </div>
                </div>
                </form>
            </div>
        </div>
    </div>
    <footer class="footer">
        <p>&copy; PicoCTF</p>
    </footer>

</div>

<script>
$(document).ready(function(){
    $(".close").click(function(){
        $("myAlert").alert("close");
    });
});
</script>
</body>

</html>

---
