# HTB CTF - FreeLancer

- CTF Name: FreeLancer
- Resource: Hack The Box CTF
- Difficulty: [30 pts] medium range

Note::: NO, I won't be posting my found FLAGS, but I will be posting the methods I used. 

<hr>


### Flag1
- Hint: Can you test how secure my website is? Prove me wrong and capture the flag! 
- Acquired By: 
 1. First thing is to look at the code and see if there is anything. This code is... painfully all in one long line, but if I search for a comment I find `<!-- <a href="portfolio.php?id=1">Portfolio 1</a> -->`, `<!-- <a href="portfolio.php?id=2">Portfolio 2</a> --> `, `<!-- <a href="portfolio.php?id=3">Portfolio 3</a> -->`, `<!-- To configure the contact form email address, go to mail/contact_me.php and update the email address in the PHP file on line 19. -->`, and `<!-- Scroll to Top Button (Only visible on small and extra-small screen sizes) -->`. Looks like a lot of comments with pages to look at and that the portfolio is using the URL parameters. These all might be exploit points.
 2. As I look at the last one about scrolling I change my screen in Developer Mode to a cell phone and see that it is just a button to take the user back to the top of the page. Not something.
 3. The next point to look at is the portfolios. If we look at the page "/portfolio.php?id=1?" we can see that it is just that image that was shown on the home page, a label, and some filler text. ![Alt Text](https://dev-to-uploads.s3.amazonaws.com/i/8rj0v5tkxq8m0nrn0r3k.png) The odd part of this is that if I visit other id numbers the first 3 are all the same and 4 and up are just the image with no text.
 4. So because these took me nowhere I now go to "mail/contact_me.php" to which is a blank page... ?? If I try and send a message on the contact page with ![Alt Text](https://dev-to-uploads.s3.amazonaws.com/i/h86lko55u8zy01vq6x51.png) I get back an error box that populates at the bottom that says "Sorry bob, it seems that my mail server is not responding. Please try again later!". This is interesting because it put in the name that I did. However, if I put a script tag in there it simply passes it back to me as text. 
 5. Now I want to go back and take a bit more time with the portfolio and see if there is some SQL that can be done. If I look at the id again and try a few different inputs I find that anything other than a ";" returns just the image with no text but if I used the ";" then I get the text back. Now I want to try something a little more so I am going to move on to SQLMap to make this quicker.
 6. For SQLmap I am going to run `python sqlmap.py -u http://docker.hackthebox.eu:xxxxx/portfolio.php?id=1 --tables` which gave me 4 databases: performance_schema, mysql, information_schema, and freelancer. Freelance looks like the one we want and it has two tables in it: portfolio and safeadmin. 
 7. Now that I know the tables I am going to change my query with SQLmap to `python sqlmap.py -u http://docker.hackthebox.eu:xxxxx/portfolio.php?id=1 -T safeadmin --dump` to specify that I know the Table and I want to dump its contents. This got me a username and password. ![Alt Text](https://dev-to-uploads.s3.amazonaws.com/i/xg4nx54zlkyx1fbhbyt7.png)
 8. Now that I have this I need to crack the password. To do this I need to crack it. For this I want to use John the Ripper. This is new to me so first I want to look at the options for it. JtR is a password cracking tool that has a lot of options, but lucky for me it also has some default settings. Once I got it installed and put the password into its own txt file I was able to run JtR with `./john /.sqlmap/output/docker.hackthebox.eu/dump/freelancer/text.txt`.  This does take a while with default settings so while this is running I also ran ZAP's fuzzer on the site to see if there were any common paths that I don't know about.
 9. Running ZAP did produce a file of /administrat that was not linked on the site and when I went there it was an admin login page. ![Alt Text](https://dev-to-uploads.s3.amazonaws.com/i/9p3px11h9g4r31vlemwg.png) When I try to run ZAP again to find more directory paths I got nothing so then I tried fuzzing for common PHP files. What I got was "logout.php" and "panel.php" but both of them show nothing when I show them in the browser. I wonder if there is a way that I can view the contents of this with SQLmap?
 10. I know that standard file directory is something like /var/www/html and then this will hold the files and pages. So for this attack I am going to use `python sqlmap.py -u http://docker.hackthebox.eu:xxxxx/portfolio.php?id=1 --file-read=/var/www/html/administrat/panel.php`. ![Alt Text](https://dev-to-uploads.s3.amazonaws.com/i/1mg0bu6umm8eqkcx3rtd.png)
 11. Now if I go to the drop location of "/.sqlmap/output/docker.hackthebox.eu" I can see a new directory and in it a new file. In that file is the php for checking if the user is logged in and there is a flag. ![Alt Text](https://dev-to-uploads.s3.amazonaws.com/i/kar2ixko5m0vhv2fz31c.png)


### Learned
This CTF taught me about using John the Ripper and SQLmap for more than just sql injection. 
As I was writing up the end of this JtR is still running and has not found any passwords ![Alt Text](https://dev-to-uploads.s3.amazonaws.com/i/gczj62s3e1p8uw8ixuom.png) This reiterates to me that JtR is not always the best tool as it can be slooooooow and there are alternatives. This also got me to use SQLmap in a way that I hadn't before and thus, expanding the knowledge around it as previously I didn't realize that SQLmap might be able to pull out files. This could lead to some interesting things... =)


<hr>

Happy Hacking
![](https://media.giphy.com/media/l3vRmVv5P01I5NDAA/giphy.gif)

### Resources:
1. https://www.openwall.com/john/


###### Please Note: that I am still learning and if something that I have stated is incorrect please let me know. I would love to learn more about what I may not understand fully.