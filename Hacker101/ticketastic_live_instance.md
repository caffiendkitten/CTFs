# Hacker101 CTF - Ticketastic: Live Instance


- CTF Name: Ticketastic: Live Instance
- Resource: Hacker101 CTF
- Difficulty: Moderate
- Number of Flags: 2

Note::: NO, I won't be posting my found FLAGS, but I will be posting the methods I used. 

<hr>



### Flag0
- Hint: 
 1. This level and the Ticketastic demo instance are running the same code
 2. Take a look at addUser on the demo instance
 3. What is missing?
 4. Humans might read these tickets and interact with them
 5. Links in tickets could be interesting
- Acquired By:
 1. For this flag the first thing that I want to do is to see if there is a "addUser" path but this returns an error of "Not Found". If I go to the admin page I can attempt to login as "admin" but the passwords I tried did nothing and there is some rate limiting so fuzzing is super slow. For now the only thing to do is submit a ticket. When I submit a ticket I get a see a simple message of "Thanks for your submission. We will get back to you as soon as possible.". hmmm not working.
 2. Normally when a user is added there is parameters like username, password, and admin that are passed so maybe I can access this page. To check this I go back to the addUser page that was not found and trying adding in the parameters into the URL `addUser?username=test&password=test&password2=test`. However, this also returned a "Not Found" page and in ZAP the response also had a 404 so this didn't work.
 3. Being that the submitting of a ticket is the only think that I seem to be able to do maybe if I submit the user to that then it will go? To do this I write a post with a title of "TEST" and then put in the body `<a href="http://localhost/newUser?username=test&password=test&password2=test">TEST</a>` The reason for this is because we want it to run locally through the localhost and create the new user there.![Alt Text](https://dev-to-uploads.s3.amazonaws.com/i/t6g6ly1rrslqecqjbh4y.png)
 4. Once we submit the ticket if we give it a minute and then go to the admin login page we can then login as the user "test" that we created. As you can see I tried a few different tickets but there is one that I did not create so let's look at that. ![Alt Text](https://dev-to-uploads.s3.amazonaws.com/i/h5r9mrue1004g2260ad9.png)
 5. When I look at this ticket I see that there are two flag but the comments around them say that one is good and one is bad.. ![Alt Text](https://dev-to-uploads.s3.amazonaws.com/i/qvct7xuwrclj5qjoeeh5.png) And in fact the first flag doesn't work and the second one does. nice.

#### Learned
This flag showed me that there are more than one way to add a user. Even if you don't have access to the "addUser" page you might be able to get the site to accept the parameters anyway.


### Flag1
- Hint: 
 1. How do others log into this instance?
 2. The login form reveals more than it should
 3. So does the ticket endpoint
- Acquired By: 
 1. First thing I did was play with the previous URL for the ticket. ![Alt Text](https://dev-to-uploads.s3.amazonaws.com/i/0ni1vskgw4qx5gtklynt.png) Here we can see that the URL is being processed because ticket 6 has not been created but it is still giving me information about a file "./main.py".
 2. Because we are able to get errors out of the server I now want to see if there is other files that could be accessed. We already know the format to add users so maybe that can be used to pull out more information about the users and their passwords. To do this I will be using SQL and the classic union. First I am going to try and determine the tables columns and schema. A standard SQL statement with union is something like `SELECT header, txt FROM news UNION ALL SELECT name, pass FROM members` and I need to make something that will concatenate the table names with the information schema. For this I needed a bit of help but came up with `ticket?id=1.1 UNTION SELECT 1,2,3--` and it produced ![Alt Text](https://dev-to-uploads.s3.amazonaws.com/i/g6ynyurkau6yqine7r0r.png).  This 
 3. Now that I know that there is SQL I want to know the version with `UNION SELECT VERSION(),DATABASE(),3--` to get ![Alt Text](https://dev-to-uploads.s3.amazonaws.com/i/0wq6a8ekewty60s54lzn.png). 
 4. Now that I know the version I can research and find that is used concatenate and create a new payload. For this payload I will use `UNION SELECT 1,GROUP_CONCAT(TABLE_NAME),3 FROM INFORMATION_SCHEMA.TABLES WHERE TABLE_SCHEMA=DATABASE()--` which gave me two table names of tickets and users ![Alt Text](https://dev-to-uploads.s3.amazonaws.com/i/3su74vw0xhk93iyfa8oo.png)
 5. Now that I know a few tables I can add the table name of "users" to the end to get the column names of that table with `UNION SELECT 1,GROUP_CONCAT(COLUMN_NAME),3 FROM INFORMATION_SCHEMA.COLUMNS WHERE TABLE_SCHEMA=DATABASE() AND TABLE_NAME='users'--` ![Alt Text](https://dev-to-uploads.s3.amazonaws.com/i/32ix6thbj5t03f9vp1ac.png)
 6. Now that I have some column names I can modify this again to find the password from the users table where the name is 'admin' with `?id=1.1 UNTION SELECT 1,password,3 FROM users WHERE username='admin'--` and there is the flag ![Alt Text](https://dev-to-uploads.s3.amazonaws.com/i/n955m9h4pv0opfyb9eb9.png)
 

#### Learned
This flag taught me more about using SQL to blindly get information out of a database. It it still something I am working on but always interesting.


<hr>

Happy Hacking
![](https://media.giphy.com/media/l3vRmVv5P01I5NDAA/giphy.gif)

### Resources
1. https://portswigger.net/web-security/sql-injection/union-attacks
2. https://www.netsparker.com/blog/web-security/sql-injection-cheat-sheet/#UnionInjections


###### Please Note: that I am still learning and if something that I have stated is incorrect please let me know. I would love to learn more about what I may not understand fully.
