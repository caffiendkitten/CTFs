
- Resource: NahamCon CTF 2020
- CTF Name: Agent 95
- Points: 50
- Category: Web

Note::: This CTF is expired and no longer live so flags will be posted.

<hr>



## Flag1
 __Hint__ 
 1. They've given you a number, and taken away your name~ 


 __Acquired By:__
### Step 1. General Look Around
When first looking at this page it can be seen that there is an extra hint given. ![Alt Text](https://dev-to-uploads.s3.amazonaws.com/i/7rac5gvcpc81amjlfr0p.png) " We will only give our flag to our Agent 95! He is still running an old version of Windows... " This hint makes me think of the "User-Agent" header that is part of the Network Request. If we look at the request in the browser we can see that currently I am labeled as "User-Agent: Mozilla/5.0 (Macintosh; Intel Mac OS X 10.15; rv:76.0) Gecko/20100101 Firefox/76.0" ![Alt Text](https://dev-to-uploads.s3.amazonaws.com/i/5o0y2wx6dnp36grofkob.png)

So what is a User-Agent anyway? A user agent is the string of information that lets servers and sites identify the application, operating system, vendor, and/or version of the requesting user. This information is used by trackers and browsers alike.

### Step 2. Can I manipulate this information?
Sure. It is super easy to change the browser information that is being sent right in the browser Developer tools. If we navigate to the tools and see the little icon in the right that has two tablet/phone little icons, where arrow 1 is point to, this will give up some options to play with, where arrow 2 is point to.![Alt Text](https://dev-to-uploads.s3.amazonaws.com/i/iofdonxtnjoj6aik2c48.png)

Arrow 2 is a drop down that will allow us to change our user-agent to technically test the site's appearance and response as different devices. This can also be used to add custom devices. 
In this case I am going to add in a custom device (so I can use it later if needed) for a Window 95 device that will be running Internet Explorer. This will need the user-agent of `Mozilla/4.0 (compatible; MSIE 5.5; Windows 95; BCD2000)` as the browser will interpret it as "Internet Explorer 5.5 on Windows 95." ![Alt Text](https://dev-to-uploads.s3.amazonaws.com/i/adxet5w3pie50vvx2m0w.png)

### Step 3. Changing the User-Agent
There are a few ways that you can change the User-Agent header: in network request in the browser, get an extension to change it, in the developer tools, or in a proxy like BurpSuite or ZAP. 

For this CTF I decided to simply use the Developer Tools in my browser and add in the User-Agent into my options so I have it for later. For this I went back to that little icon that arrow 1 iis pointing to above and then in the drop down that arrow 2 is pointing, navigate to the drop down and select the "edit list" option. ![Alt Text](https://dev-to-uploads.s3.amazonaws.com/i/b4vyeh4v626ov237ze5t.png)

Now that you are in the list you can see that there are a lot of options, but I want to check out the "Add Custom Device." ![Alt Text](https://dev-to-uploads.s3.amazonaws.com/i/fyz2fj1y2yzyx9gafslq.png) This will give options 
From here I can add in a custom User-Agent and save it for later use. ![Alt Text](https://dev-to-uploads.s3.amazonaws.com/i/maise3zc7zfhuoxua79g.png)

### Step 4. The Flag
Now that I have my Windows 95 User-Agent set up I can select it, refresh the page, and get the flag. ![Alt Text](https://dev-to-uploads.s3.amazonaws.com/i/hv21mle4ko26a5fy5wqf.png)




## Learned
This flag showed me more about using the User-Agent header and how it can be used to display different information. 
Generally the User-Agent is just information for how the site will be displayed to you and this is good. According to MalwareBytes Labs, "sites with malicious intentions can use this information to deliver specific exploits that have a bigger chance of working on your system. But there are more refined ways to do this, that get far more useful information. Also, it is not that hard to adapt your user agent string, so if you want to mislead the webserver that is not very hard either." (4)

Though, it is generally just information and in this specific case it showed something interesting, there have been CVEs that have been posted exploiting the User-Agent header. For example, Shellshock CVE-2014-6271, CVE-2014-6277, CVE-2014-6278, CVE-2014-7169, CVE-2014-7186, CVE-2014-7187 might be exploitable with the User-Agent. ![Alt Text](https://dev-to-uploads.s3.amazonaws.com/i/l8ecyg577d3amt4tx0ve.png) Check out link #5 for more information.







<hr>

Happy Hacking
![](https://media.giphy.com/media/l3vRmVv5P01I5NDAA/giphy.gif)

### Resources
1. https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/User-Agent
2. https://github.com/tamimibrahim17/List-of-user-agents
3. https://developers.whatismybrowser.com/useragents/parse/
4. https://blog.malwarebytes.com/security-world/technology/2017/08/explained-user-agent/
5. https://betanews.com/2017/03/22/user-agent-based-attacks-are-a-low-key-risk-that-shouldnt-be-overlooked/


###### Please Note: that I am still learning and if something that I have stated is incorrect please let me know. I would love to learn more about what I may not understand fully.
