# Port Swigger Academy CTF - CSRF vulnerability with no defenses

Today we take an adventure into Post Swigger's Web Security Academy and their process. 
The nice thing about this is that it feels more practical. There are no "flags" like traditional CTFs. This is all focused on practical outcomes and actual issues. 
The annoying thing is that it gives no hints as to how to start so I had to Google a LOT to just figure out how it wanted me to use their system.

<hr>

- CTF Name: CSRF vulnerability with no defenses
- Resource: Web Security Academy
- Difficulty: Easy
- Number of Flags: 1

<hr>

### Flag0
- Hint: None. Point of the lab is to construct a web page that will launch a CSRF attack and change the users email address.
- Acquired By: To change the users email address simply log in with the creds that were given (carlos/ montoya). 
 - Once logged in, navigate to the "change email" tab ![Alt Text](https://thepracticaldev.s3.amazonaws.com/i/azwhf84emcrw3gi8qb0c.png)
 - Now lets see what it happen in the page source and the network. ![Alt Text](https://thepracticaldev.s3.amazonaws.com/i/vv5t2nrk6hvd8haivsht.png)![Alt Text](https://thepracticaldev.s3.amazonaws.com/i/ho7eixp7wmpabuyzk81w.png) There isn't much going on here. The only thing being sent in the network body is the form data with an email address that will be changed. It is a "POST" method and there is a cookie in the Request header. (You can also see this in BurpSuite but the network tab is just as easy and no proxy needed. ![Alt Text](https://thepracticaldev.s3.amazonaws.com/i/4rb5h8quici4ai5n63nv.png))
 - Now jump over to the "exploit server" at the top of the page and craft the body of the exploit. Below you can see that I took the code they had in the main info part and changed it a bit for my instance.
![Alt Text](https://thepracticaldev.s3.amazonaws.com/i/e1zoft60cfbv55rncdr2.png)
 - Now save it via the "store" button as this will automatically launch it and you pass the lab.
- Thoughts:This took me a minute to wrap my head around what exactly they were wanting. For this lab you need to be in their environment and there is really only one way to solve it. Utilize their "exploit server" with the correct body.  I originally was trying to run this exploit by crafting a .html page myself but that wasn't working. So far it seems that they want you to use their exploit server system to solve the task but it still shows a practical use of the process.




### Learned
CSRFs can not look like much but can be very costly if executable. When successful a CSRF attack causes the victim to carry out an action unintentionally and depending on the nature of the action, the attacker might be able to gain full control over the user's account.

### A way to protect against this attack? 
A common way to avoid this attack is by having a CSRF Token. BUT, this is not all that is needed. I'll be explaining more in the next step of this lab set/CTF.


Happy Hacking
![](https://media.giphy.com/media/l3vRmVv5P01I5NDAA/giphy.gif)

###### Resource:
1. https://portswigger.net/web-security/csrf

###### Please Note that I am still learning. If something that I have stated is incorrect please let me know. I would love to learn more about what I may not understand fully.
