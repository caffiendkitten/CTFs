# Hacker101 CTF - Petshop Pro

- CTF Name: Petshop Pro
- Resource: Hacker101 CTF
- Difficulty: Easy
- Number of Flags: 3

Note::: NO, I won't be posting my found FLAGS, but I will be posting the methods I used. 

<hr>


### Flag0
- Hint: Something looks out of place with checkout/It's always nice to get free stuff
- Acquired By: This flag was acquired by modifying the form input that gets submitted on the checkout page.
  - ![Alt Text](https://thepracticaldev.s3.amazonaws.com/i/0nseio9tba6mulea6ub9.png)If you inspect the checkout page you can see that there is a hidden field. 
 - By removing the ```type="hidden"``` from the page you are able to bring the input field to the page to edit. ![Alt Text](https://thepracticaldev.s3.amazonaws.com/i/1ltokxtqigdoimipeu2k.png) 
 - Now that we have the field on the page it will look like this: ![Alt Text](https://thepracticaldev.s3.amazonaws.com/i/3v44f0t6gr59oxhnro83.png) 
 - Once the input field is edited properly hit that check out button and poof. flag. ![Alt Text](https://thepracticaldev.s3.amazonaws.com/i/7brykkszn7ir0ab7j7x5.png)
- Thoughts/Learned: This exercise gets you to look at all parts of the page. There can sometimes be hidden fields that are doing things in the background that help users but also are vulnerable attack points. If this were a real site it would allow users to change the price they paid for an item and potentially get things for free or worse, use it as an injection point for something more malicious.

### Flag1
- Hint: There must be a way to administer the app/Tools may help you find the entrypoint
- Acquired By: 
 - First, check out the URL. There is a pattern with the /cart. By using the /login you are able to get an admin login page.
 - Now, by navigating to the /login page you are able to see the admin login. From here I attempted a few admin:admin and such pairs but no success. From this point I will a tool to try some username/password pairs. ![Alt Text](https://thepracticaldev.s3.amazonaws.com/i/auk0cck1t6gtp2fhsl8h.png)
 - First I downloaded my own username list and tried it in my Community Edition of BurpSuite. ![Alt Text](https://dev-to-uploads.s3.amazonaws.com/i/huzs6yyop4vsnjirk9vf.png)Unfortunately, with the community edition it is a throttled project and based on cheating and looking up how someone else solved this, this would not be a good option as the username was someone in the 8000+. Using this method would take too long so I tried ZAP
 - ZAP I am not as familiar with so this took some playing. First I tried a simple attack but that didn't produce anything. I did not modify anything with this because I wanted to see what it would do on its own... which was nothing.
 - Now I tried the ZAP fuzzer and I was not able to get it to run on the first try but after some tweaking the words that would be tried for the username and password, I was. ![Alt Text](https://dev-to-uploads.s3.amazonaws.com/i/pgdqh8wynmfusgmxph1z.png)However, the username and password rotate, so I needed to set this up to run and let it do its thing.. Let me tell you, this takes so long. ![Alt Text](https://dev-to-uploads.s3.amazonaws.com/i/nssurm3b6lkisnb5hnuk.png) I attempted this a few times with different lists but nothing produced to be anything useful.
 - Time for a new approach. Hydra. Hydra is a password cracking tool.  This again took some tweaking but let me show the main part of the script... <br> `hydra -L rockyou.txt -p aaa  35.xxx.xxx.xxx http-post-form "/73fxxxxxxx/login:username=^USER^&password=^PASS^:Incorrect password"` Here Hydra basically iterates through all the username/password combinations, until it gets a response that __does not__ contain the text “Incorrect username”. After trying a few lists with this I was able to product a username.![Alt Text](https://dev-to-uploads.s3.amazonaws.com/i/oiewqst3top55z5osgbj.png)
 - After acquiring a username switch up some of the script to use that username and now test for the password. `hydra -l name-you-found -P passwords.txt  35.xxx.xxx.xxx http-post-form "/73fxxxxxxx/login:username=^USER^&password=^PASS^:Incorrect password".` This one is taking longer than the names soooo now I wait.
 - Once the username and password have both been cracked you can login and see the flag right there.![Alt Text](https://dev-to-uploads.s3.amazonaws.com/i/rre89mgci7cu8kuebeda.png)
- Thoughts/Learned: 
This lab was... a test of patience. Choosing the wrong list will waste a lot of time. It might be worth testing for common usernames and passwords but it is a tedious task. I'm glad I learned more about Hydra and ZAP fuzzer though.





### Flag2
- Hint: Always test every input. Bugs don't always appear in a place where the data is entered
- Acquired By: <br>
This flag you will need to be logged in as.
 - First thing to do it test all of the inputs. I ran around before logging in and put `<img src=x onerror=alert(1)>` int anything I could. I did get a lot of alert boxes but none that produced a code. 
 - Once you get some login creds try this again. Once you get an admin access there are more input points for each item for purchase. 
 - Choose your favorite picture and edit it. This will show you a few options. ![Alt Text](https://dev-to-uploads.s3.amazonaws.com/i/0c9ji90433aoahj1wy87.png)As you can see, I chose the cat. Now save it. You might get an error so try to remove one of the image error scripts and try saving again. 
 - Now that it is saved, add that item to your cart and go view your cart for the flag. ![Alt Text](https://dev-to-uploads.s3.amazonaws.com/i/amvvhv81r5ro2h5z696u.png)
- Thoughts/Learned: This one was a tad tricky because it wasn't clear that it's goal was to be logged in. Oh well. that's how this goes sometimes. You try everything and then change something and then try everything again. patience!!!




### Final Thoughts
This CTF was difficult for a lot of reasons. The most annoying reason was the rotating credentials. I have yet to see a CTF that does this so it was a new thing for me. It did get me more used to using ZAP and Hydra so that was overall great.

<hr>

Happy Hacking<br />
![](https://media.giphy.com/media/l3vRmVv5P01I5NDAA/giphy.gif)

###### Please Note that I am still learning and if something that I have stated is incorrect please let me know. I would love to learn more about what I may not understand fully.
