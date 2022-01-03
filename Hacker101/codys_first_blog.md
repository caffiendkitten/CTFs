# Hacker101 CTF - Cody's First Blog

- CTF Name: Cody's First Blog
- Resource: Hacker101 CTF
- Difficulty: Moderate
- Number of Flags: 3

Note::: NO, I won't be posting my found FLAGS, but I will be posting the methods I used. 

<hr>



### Flag0
- Hint: 
 1. What was the first input you saw?
 2. Figuring out what platform this is running on may give you some ideas
 3. Code injection usually doesn't work
- Acquired By:
 1. For this flag the first thing that I want to do is open ZAP and see what the network request and response are on refresh. This shows me that the site is running PHP 5.5.9 and Nginx 1.14.0. So, the webpage is using php to communicate with the Nginx server. Cool. 
 2. According to CVE Details php 5.5.9 has a LOT of security issues and Remote Code Execution seems to be the most popular exploitable thing so let's try that. For this I want to put a simple php script into the comment box. ![Alt Text](https://dev-to-uploads.s3.amazonaws.com/i/zmkr55diowyv67pa61g0.png). This populated a new page with the flag.![Alt Text](https://dev-to-uploads.s3.amazonaws.com/i/daoih7oh47tl2mo2zwz5.png)

#### Learned
This flag taught me about paying attention to the network requests to to see what information is coming back from the server. This specifically allowed me to determine the versions of php that was talking to the server and run some RCE script. Once RCE is achieved with simple code malicious code could be used to try and get more information out of the server or take over accounts.

### Flag1
- Hint: 
 1. Make sure you check everything you're provided
 2. Unused code can often lead to information you wouldn't otherwise get
 3. Simple guessing might help you out
- Acquired By: 
 1. First thing I did was look at the code for site as I know that the hint is talking about unused code. In the code I find `<--<a href="?page=admin.auth.inc">Admin login</a>-->`. 
 2. Now that I have a place to do I add that to the URL line and see the admin login page. ![Alt Text](https://dev-to-uploads.s3.amazonaws.com/i/3ieubhtxaur9jxli3t3n.png) Now that I have a login page I checked again to see if there was anything else in the code but nothing was there. 
 3. Now I need to play with the login. For this I am going to try some simple SQL Injections for admin as the username and try and avoid the password. ![Alt Text](https://dev-to-uploads.s3.amazonaws.com/i/f5r1gy0sppej00v8t1iu.png) but this produced some lovely red font saying " Incorrect username or password". Now I am going to use ZAP to resend a few variations of username and password combinations. However, nothing popped so I went back to researching php.
 4. In php 5.5.9 there is an issue around the admin/auth.inc file. It allows SQL commands to bypass authentication. For this exploit I searched for the original path that I was given of `admin.auth.inc` and found that if I deleted the "auth" that it bypassed login and took me directly to the page with the flag.![Alt Text](https://dev-to-uploads.s3.amazonaws.com/i/solwg1tfux5am3l5nhwu.png)

#### Learned
This flag taught me about php 5.5.9 and the idea of modifying the URL path to bypass authorization. It seems odd that this could be a thing that is possible but it worked.

### Flag2
- Hint: 
 1. Read the first blog post carefully
 2. We talk about this in the Hacker101 File Inclusion Bugs video
 3. Where can you access your own stored data?
 4. Include doesn't just work for filenames
- Acquired By: 
 1. If we go back to the home page we can see a comment in the first post about an "include()". ![Alt Text](https://dev-to-uploads.s3.amazonaws.com/i/a0m5ki1gicgv2gkqjtzz.png) This makes me think that php has an include() option and some Googling says it does and there is an issue with it. To test this by modifying the URL we can get an error is we change the page we are "include()"ing. ![Alt Text](https://dev-to-uploads.s3.amazonaws.com/i/69xr1q6ak02vd6g4pwxr.png)
 2. Now that we know that there is some file inclusion in the URL path we can see if it will also accept file traversal of the server. To do this I tried the common "../../../etc/passwd", but that did not work.
 3. If we look at the hints it is talking about where we access our own stored data and how filenames don't just work. This tells me that I will need to try something else. So, if I wanted to communicate with this server I would access the LocalHost so I tried that but got an error.![Alt Text](https://dev-to-uploads.s3.amazonaws.com/i/mxd16ao8ergp8ro3hh5p.png)
 4. Now that I know SOMETHING is happening I want to play with this and try localhost/index but with or without the "--" it either took me to the regular homepage or gae me some errors at the bottom of the page about each of the comments that I had posted that failed in some way. hmmm
 5. I feel like I am on the right path but "where can I access my stored data"... ?? OK, I know that the URL path has file access and that I can use the include function to get info out of the server. What happens if I make a comment to echo the contents of the index file? In theory, this will allow me to see what code is running on the page and see if there is something I am missing. To do this I am going to make a simple comment of `<?php echo readfile("index.php")?>`. This produced nothing but I also probably need to make the admin approve the post so back to the admin page. ![Alt Text](https://dev-to-uploads.s3.amazonaws.com/i/ed1h0udopp937f3fuh5i.png)
 6. Once the comment is approved and I go back to the home page I see... no change. hmm. If I go to the http://localhost/index I still see no change. I am missing something.
 7. If we look at the image I just posted we can see that the location of the file is not on the homepage so it will not show up when I go back to the homepage OR when I go to that page. I need to resubmit the comment on the homepage and then see if I can get the index.php contents. ![Alt Text](https://dev-to-uploads.s3.amazonaws.com/i/99nlfifv5q2gqx54ueqr.png)
 8. Once I posted the comment on the homepage and approved it and go back I see I am still missing something. If I go to the index.php page I get an error of " Fatal error: Allowed memory size of 134217728 bytes exhausted (tried to allocate 16384 bytes) in /app/index.php on line 20"...
 9. If I go back to the `?page=http://localhost/index` I can see that I am displaying all the comments that I submitted. And if I look at the page source I can see all the php comments I submitted and the flag.![Alt Text](https://dev-to-uploads.s3.amazonaws.com/i/heb8ehe024fh8hstxd3f.png)

#### Learned
This flag taught me more about php, the php file inclusion bug, and how php is used to not only display a webpage but also that it's communicating with the server. 

I also found it an interesting way to view the php in a page. PHP is handled server side (before it's sent to the user) but by putting the payload in the comments you are able to then utilize the `index.php` page's `include()` function to make a request to the local `index.php` file because of the vulnerabilities that this version of php.

> "The PHP language has a directive which, if enabled, allows filesystem functions to use a URL to retrieve data from remote locations. The directive is allow_url_fopen in PHP versions <= 4.3.4 and allow_url_include since PHP 5.2.0. In PHP 5.x this directive is disabled by default, in prior versions it was enabled by default." (4)

This works because of the file path that the server is trying to access (`?page=http://localhost/index`). Similar to accessing a local file on your local browser or in the file directory, the server wants to include the page it is told to but when it makes a request to get the page `http://localhost/index` the server processes this as its own index page (because of the bug), adds the `.php` to the end, and renders the requested page’s source in the main page's source code though the PHP `echo readfile()` command.


#### Remediation:
Though the most effective solution would be to not allow user-submitted input into a filesystem/framework this is not usually practical. (4)
Keeping systems up-to-date, validating all user input, and even better would be to maintain a whitelist of acceptable files to access through the use of an identifier, such as index/key number and thus rejecting any request containing an invalid identifier. (5)


<hr>

Happy Hacking
![](https://media.giphy.com/media/l3vRmVv5P01I5NDAA/giphy.gif)

### Resources
1. https://www.cvedetails.com/version/164957/PHP-PHP-5.5.9.html
2. https://www.cvedetails.com/cve/CVE-2006-2700/
3. https://www.youtube.com/watch?v=ehp9TdmXWr0
4. https://en.wikipedia.org/wiki/File_inclusion_vulnerability
5. https://owasp.org/www-project-web-security-testing-guide/assets/archive/OWASP_Testing_Guide_v4.pdf

###### Please Note: that I am still learning and if something that I have stated is incorrect please let me know. I would love to learn more about what I may not understand fully.
