# Hacker101 CTF - Micro-CMS v2

More and more CTFs!!!!

Recently I've started diving into CTFs and trying my hand at some Bug Bounties. This means that I will need to be writing reports with any bug I find and want to practice. So, here I go. 

- CTF Name: Micro-CMS v2
- Resource: Hacker101 CTF
- Difficulty: Moderate 
- Number of Flags: 3

Note::: NO, I won't be posting my found FLAGS, but I will be posting the methods I used. 

<hr>

### Flag0
- Hint: Regular users can only see public pages, Getting admin access might require a more perfect union
- Acquired By: This one seriously too me two days. I spent time reading over how people handled it and nothing really made since until today. SO, if you try to log in as 'admin' and a few different passwords you get back "404  Not Found". This is where throwing in some SQL helps. At this point I tried ```admin '``` and got back ![Alt Text](https://thepracticaldev.s3.amazonaws.com/i/0o4tqcn2i7rar752cioz.jpg). This is interesting and all. It tells me that I am on the right track but I don't have the correct format. The hint is about a union so I think I need a SQL UNION of some kind. From here I used the string ```'UNION SELECT '123' AS password FROM admins WHERE '1' = '1'``` and came back with errors. If you read the last line of the image above each time you try a different SQL string, it changes. For me, this is where I got stuck. Nothing seemed to work and all of the videos showed Burp Suite or it just working and nothing made since to me. It seemed like they were doing something that wasnt being shown but it was really just bad videos and lack of my own knowledge of what was happening. It took me about half a day more to really nail down how to use Burp for this and get it working. So let me explain it. 
 - To use Burp here we need to access the page with the proxy running on the site. From here I could see my string in the Proxy tab and HERE is where you need to sent it to the Repeater. Once in the repeater you can refine the SQL sting and for me I found out that I needed to get rid of my last '. I was able to have ```'UNION SELECT '123' AS password FROM admins WHERE '1' = '1``` as the username and the password was the 123 without quotes. This produced the proper response in Burp. From here I was able to take this new working SQL string to the real site and log in. Now navigate back to the hope page and the "Private Page" is there with the key. ehhh I'm tired now but I have two more to do. ![Alt Text](https://thepracticaldev.s3.amazonaws.com/i/bpg33517e171y7mnrwo6.jpg)
- Thoughts: Pay attention to your quotes. Sometimes more is not better.

### Flag1
- Hint: What actions could you perform as a regular user on the last level, which you can't now?
- Acquired By: Utilizing CURL in the command line made this way easier than Burp Suite. ![Alt Text](https://thepracticaldev.s3.amazonaws.com/i/2mk0ehuvs9bys2f7lhgf.jpg) As you can see you will need to change the specific URL to your specific instance path but it was as easy as this.
 - FYI for Curl the following tags are added and here is what they mean.
 - -v, --verbose       Make the operation more talkative
 - -X, --request <command> Specify request command to use
 - POST forces the URL as a POST request rather than the GET that it is normally.
- Thoughts: If I thought the previous flag had confusing walk throughs this one was WAY worse. I attempted so many Burp Proxy and Repeaters and nothing was proving responsive... until I found one that went into the CLI and used Curl.
 - I am also not entirely sure what this does. I get that I can force a different request to the API than it is expecting and get a different response but I am not sure how this is practical... I will have to play with this more.


### Flag2
- Hint: Credentials are secret, flags are secret. Coincidence?
- Acquired By: This took some fancy SQL that I would never have guessed... How is this a medium difficulty? Anyway... First we need to apply ```username=foo' OR (select 1 from(select count(*),concat((select (select (SELECT concat(0x7e,0x27,cast(admins.username as char),0x27,0x7e) FROM `level2`.admins LIMIT 0,1) ) from information_schema.tables limit 0,1),floor(rand(0)*2))x from information_schema.tables group by x)a) AND '1' = '1``` this string to the username. wow. It produces for me ![Alt Text](https://thepracticaldev.s3.amazonaws.com/i/02ngmd57vo6tj7yprsa0.jpg). I need traci. Then I need another crazy string ```username=foo' OR (select 1 from(select count(*),concat((select (select (SELECT concat(0x7e,0x27,cast(admins.password as char),0x27,0x7e) FROM `level2`.admins LIMIT 0,1) ) from information_schema.tables limit 0,1),floor(rand(0)*2))x from information_schema.tables group by x)a) AND '1' = '1``` to produce ![Alt Text](https://thepracticaldev.s3.amazonaws.com/i/2k8q7d5avvf6v7ypo7fs.jpg) and again I need to pay attention to the Duplicate Entry of "Larissa". (I assume that these will be different for anyone and either way you should do this yourself to see the results as they appear on your system).
This means that for me my secret credentials are traci with a password of larissa and then the key appears. 
- Thoughts: A lot of people used Burp Suite to get this one but I had a lot of difficulty figuring out exactly they were doing do I went this rout with SQL, though admittedly I am not entirely sure what it is doing. I can tell that it is concatenating the admin password  and getting information from "level 1" (wherever that is) and limiting the the returned value from the information_schema.tables group... I can guess that it is designed to pull information from each part of the table to find a hidden log in and its associated password but I will have to do more research as to how. If you know more about this I would love to get your input.




Happy Hacking
![](https://media.giphy.com/media/l3vRmVv5P01I5NDAA/giphy.gif)

###### Please Note that I am still learning. If something that I have stated is incorrect please let me know. I would love to learn more about what I may not understand fully.
