- CTF Name: TempImage
- Resource: Hacker101 CTF
- Difficulty: Moderate
- Number of Flags: 2

Note::: NO, I won't be posting my found FLAGS, but I will be posting the methods I used. 

<hr>



### Flag0
__Acquired By:__
 1. Like other flags the first thing I want to do is look at the code. There doesn't first seem much. It seems to be a php page as there is a link to a php page. ![Alt Text](https://dev-to-uploads.s3.amazonaws.com/i/cuytl1qndf238zxenqrg.png) 
    The network response shows that there is a a nginx1.14.0 (Ubuntu) server running and php/5.5.9-1ubuntu4.24 but that is about it. ![Alt Text](https://dev-to-uploads.s3.amazonaws.com/i/a03b5vwuu1cb81f1g2a4.png)
 2. Navigating to the upload.php page there is a hidden field in the code and it looks like that script will display the filename on the screen. ![Alt Text](https://dev-to-uploads.s3.amazonaws.com/i/8uhbkilxqgcohucv7752.png)
    By trying to add in a pic of the wrong type I can see the file name is displayed on the screen. ![Alt Text](https://dev-to-uploads.s3.amazonaws.com/i/0ncmjz5feyxudzr91uxr.png)
    By sending the image in I get an error that the image I tried to upload is not supported. ![Alt Text](https://dev-to-uploads.s3.amazonaws.com/i/6m2z79s565htqvpqci8t.png)
 3. Going back and adding in a picture of the supported type the filename is still shown on the page and this time the image uploads with no problems. ![Alt Text](https://dev-to-uploads.s3.amazonaws.com/i/8fqtpjsrhbfanbp91rxu.png)
    Looking at the URL we can see there is an added section for "files" now in the path of the image. `http://35.227.24.107/9a1694927f/files/4a47a0db6e60853dedfcfdf08a5ca249_1.png` If I modify that to be `flag` instead of the image I get an error that the file is not found. ![Alt Text](https://dev-to-uploads.s3.amazonaws.com/i/0o82jeasvve2q8zrgou2.png) But there might be other files...
 4. Before I try to find other files I want to see what happens if I upload the same image again. By uploading the same image again I get the same url... That seems odd and I would assume that each image would get a new string on the front of it. If I toss this string over to [https://crackstation.net/](CrackStation) I can see that it is an MD5 hash for the file's name. ![Alt Text](https://dev-to-uploads.s3.amazonaws.com/i/nxgo4jv0ut6dfe3qzo3v.png)
 5. Now that I know it's format I want to see what if I upload a file path as the file name ending in .png. For this I uses a php file that was made to get the phpinfo(). I named the file `/../../../etc/passwd%00.png` and sent it in. On the first try I can see that the site is not allowing me to use '/' as part of a file name and is changing it so this attack didn't work. ![Alt Text](https://dev-to-uploads.s3.amazonaws.com/i/0j1klr3sm05uzxefzeum.png)
   If I Repeat the request but modify it so that the file name is correct you can see that the flag populates.![Alt Text](https://dev-to-uploads.s3.amazonaws.com/i/l1zqg0c0pui60qjgiflx.png)

__Learned/Root Cause__
This flag is dealing with an attackers ability to gain access to arbitrary files on the server that is running a web app though directory traversal.
In this case I was able to bypass the applications validation for a specific file type (.png) by requesting one that would lead to a different path. 

__Remediation__
The best Remediation is not allowing user input when using file system calls.
If this is not possible then validating the users input, use of policies around where files can be saved and what files can be obtained, using whitelists to ensure user input is only of allowed content, and normalizing user input before using.



### Flag0
__Hint__ 
 1. It clearly wants one specific format
 2. If you can't bypass that check, what can you do?
 3. Read up on PNG chunks

__Acquired By:__
 1.  Because this is part 2 I won't look at the code. It hasn't changed. So this time I want to see if I can upload a malicious PNG file. This is a file that is a PNG, but it will have some code in it that will run instead of or with the image. 
   To do this I start off with a simple .png file that will test if it will accept other input. But as you can see having php code in the image contents was a fail... ![Alt Text](https://dev-to-uploads.s3.amazonaws.com/i/962r3bv2k2f5ujr6o3zr.png)
 2. It appears that the application might be checking if the contents of the image are really image data so now I am going to modify this payload to be expected image data.
    To do this I will take a small image I have and upload it once. ![Alt Text](https://dev-to-uploads.s3.amazonaws.com/i/5zb97a1yusiu41uovfs8.png) 
 3. And now in BurpSuite I will Repeat the request but I will add some data to the png chunks.
    First thing to do is to get it working. For this I will add ``<?=`$_GET[0]`;?>`` to the file and send that. ![Alt Text](https://dev-to-uploads.s3.amazonaws.com/i/bjaay1qtr3b3llvk18g6.png)The output is a page with the text path on it... hmmm![Alt Text](https://dev-to-uploads.s3.amazonaws.com/i/e1wdr7xc6en7u2j35yeu.png)
 4. Now that I have a GET in the code and I can see it was accepted by the status 302 and the page loading I now need to modify this a bit. The application is only checking the `Content-Type: image/png` and now the end file type so I will change that to .php. ![Alt Text](https://dev-to-uploads.s3.amazonaws.com/i/20dyu3qdhh47ubwn0dl1.png)
    Nothing really changed but now if I go to the new php file location I have the option to add in some parameters `1.php?0=ls` .![Alt Text](https://dev-to-uploads.s3.amazonaws.com/i/iuv5yfvyrfs26ko2i4jd.png) As you can see I added in a lot of files. Now let's replace the `ls` with a `cat filename` and take a look at a few of them.
 5. First one (default.conf) is not interesting.. ![Alt Text](https://dev-to-uploads.s3.amazonaws.com/i/t1fmm6uio137r9wr1nxs.png)
   Second one (index.php) is not that interesting... ![Alt Text](https://dev-to-uploads.s3.amazonaws.com/i/tcqu8jvafh6689vo09ev.png)But if we look at the response that the network got we can see the flag. ![Alt Text](https://dev-to-uploads.s3.amazonaws.com/i/6mmxukq96wtsu6azvmhf.png)

__Learned/Root Cause__
The vulnerability is the ability for an attacker to bypass weak validation and inject malicious file to get execution during processing.
This is a really interesting issue as it seems like it could be difficult to check for. 

__Remediation__
Recommended remediation is to make sure the web server does not parse or execute any code in the directory or by the web-browser, if possible, store user-uploaded images outside of the web-root directory or on a different/additional (CDN) server, and validating file names and whitelisting acceptable characters.





<hr>

Happy Hacking
![](https://media.giphy.com/media/l3vRmVv5P01I5NDAA/giphy.gif)

### Resources
1. https://www.w3.org/TR/PNG/
2. https://www.w3.org/Graphics/GIF/spec-gif89a.txt
3. http://www.matthewflickinger.com/lab/whatsinagif/bits_and_bytes.asp
4. https://www.idontplaydarts.com/2012/06/encoding-web-shells-in-png-idat-chunks/
5. https://blog.isec.pl/injection-points-in-popular-image-formats/

###### Please Note: that I am still learning and if something that I have stated is incorrect please let me know. I would love to learn more about what I may not understand fully.
