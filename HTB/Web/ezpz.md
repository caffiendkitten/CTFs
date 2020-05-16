# HTB - ezpz


- CTF Name: ezpz
- Resource: Hack The Box CTF
- Difficulty: [20 pts] For me this was kind of easy but not super easy.
- Number of Flags: 1

Note::: NO, I won't be posting my found FLAGS, but I will be posting the methods I used. 

<hr>

### Flag1
- Hint: <!-- Hint : base64_encode($data)-->
- Acquired By: 
 - The first thing that I noticed was that page seemed like it was broken...![Alt Text](https://dev-to-uploads.s3.amazonaws.com/i/cm4dpo4puwk79y5am4rx.png) BUT if you look at the source page you can see that waaaaaaaaay at the bottom on line 103 there is a hint. (this can also been seen by curlinging the site with `curl http://docker.hackthebox.eu:yourportnumber`)
 - Next is to fuzz the general site and see if any common pages pop up. For this I used ZAP Fuzzer and it actually got a bunch of false positives.![Alt Text](https://dev-to-uploads.s3.amazonaws.com/i/jnks9039tdhwd7ft2w8c.png) You can see, the response of all the payloads that were accepted all have the same response, so this isn't going to produce anything useful.
 - I did take the next step to "attack" the site with ZAP Attack just to see if anything else showed up but that also didn't produce anything useful.
 - Next step is to see if there is anything in the headers being sent that could be changed to get access. The hint is about base64 so I want to go over the headers and see if anything can be decoded. For this I will just be using the browsers Network tab in the Developer Tools. ![Alt Text](https://dev-to-uploads.s3.amazonaws.com/i/5mwoav6dyum9wgl0xmab.png) There is a cookie but after looking at them they don't have anything in them.
 - On going back to the site to look at it I realize that it  has an error on the page. "Notice: Undefined index: obj in /var/www/html/index.php on line 27" When you change the URL to "http://docker.hackthebox.eu:yourportnumber/?obj=hi" the error goes away. 
 - Now I am going to test this object and see if anything populates differently. Because it is also talking about a 'ID' property I want to try a standard parameter of '{"ID":1}'. This didn't change anything BUT then I remembered the hint about Base64 encoding the data. Once I took the parameter and Base64 encoded it the page changed. Now both errors are gone and I have a new message.![Alt Text](https://dev-to-uploads.s3.amazonaws.com/i/f12i3tht30dmfimj3tl0.png) Interesting.... This tells me that I am on the right track BUT I am missing something. I wonder if I can add in the SQL injection and get more information out of it?
 - Before I go down that route I want to try a few other IDs and see if anything else poplates. I discovered that if I put in another ID number I got a new message.![Alt Text](https://dev-to-uploads.s3.amazonaws.com/i/z5tp9tc9g39s3yrrgnl4.png)
 - I now want to try some SQL injection. To do this I want to first try some punctuations and see if anything changes. The standard `":';tick ` will be used. Once I tried an ID of a single quote I got a new message. ![Alt Text](https://dev-to-uploads.s3.amazonaws.com/i/rl4xl73d4hczg2h7ll0k.png) This tells me that I need some kind of mySQL string.
 - Time to do some research on mySQL as I am not super familiar with constructing strings with it. I want something to try and find the table with information about the schema. In MySQL this is called the informaiton_schema. But, it's not just as easy as encoding {"ID":informaiton_schema" it needs more to break out of the expected ID number and combine the information schema to the output. This means I need to construct a UNION of some kind. 
 - My first attempt was just adding in a UNION of all `'UNION SELECT *` but this produced the same error as before. 
 - My second attempt was based on PortSwiggers "SQL injection UNION attacks" lab. This involved me playing with it a lot to see what would populate, if anything. I recommend going through this lab to practice. See reference #1 below for this link. As for me I eventually got something like swisskyrepo payload (`SELECT 1,2,3,4    -> UNION SELECT * FROM (SELECT 1)a JOIN (SELECT 2)b JOIN (SELECT 3)c JOIN (SELECT 4)d`) to populate. I used a the `' UNION SELECT * from (SELECT 1)a JOIN (SELECT column_name from 'information_schema'.'columns')b#` and got a plethora of information. ![Alt Text](https://dev-to-uploads.s3.amazonaws.com/i/on3jytf0ly4vuubmhm4w.png) 
The trick with this is that the error message is saying that it is expecting, at least, 2 variables by saying that "mysqli_fetch_assoc() expects parameter 1 to be mysqli_result." This tells me that I need to "SELECT 1,2" like swisskyrepo's select statement. Furthermore, I used it this specifically because the columns in the information_schema "provides information about columns in tables" and I want to see all the tables available. (3)
 - With this information I see that in the list there are a few columns with the name "flag", "flAgEzPZSQLi", "password_expired", "dummy", and "SQL_DATA_ACCESS". Though these look interesting and I want to try and see them, I want to see what was lastupdate first. For this I will be using my working string from before and modifying the second part. Something like `' UNION SELECT * from (SELECT 1)a JOIN (SELECT table_name from mysql.innodb_table_stats)b#`. Why "table_name from mysql.innodb_table_stats"? I chose this for the second SELECT because I now have a list of Table Names to choose from and after a bit of research I found out that the "mysql.innodb_table_stats" "and innodb_index_stats tables include a last_update column that shows when index statistics were last updated". When I tried this string I got back some information about a DATA table and more. ![Alt Text](https://dev-to-uploads.s3.amazonaws.com/i/qzwaa310y6ojoe56fkla.png)
 - With this information I now know that "FlagTableUnguessableEzPZ" was updated, but this was not in the list before... This is because the first list gives me information about the columns and this now gives information about tables.
 - Now I want to try my payload again but I want to look at this table now. For this I will use `' UNION SELECT * from (SELECT 1)a JOIN (SELECT table_name from ezpz.FlagTableUnguessableEzPZ)b#` but this isn't quite right. I am getting the same error as before.
 - Second do I changed the "table_name" to "*" as I want to see everything and not just the table name. `' UNION SELECT * from (SELECT 1)a JOIN (SELECT * from ezpz.FlagTableUnguessableEzPZ)b#` and the flag populates right on the screen. 
![Alt Text](https://dev-to-uploads.s3.amazonaws.com/i/g97r1reyiksrwt1q226y.png)


### Learned
This flag really taught me the most about SQL injection and testing it. This flag was tedious as it took a bit of playing with the specific ways that SQL payloads need to be input. This is something I am not string with so it was the most difficult part.


### RCS
What is the root cause of this issue? Though I am not sure yes as to the specific root cause I can see that there is an issue with the URL parameter and the server exposing too much information. Because it appears that the parameter is just being accepted without any cleaning up of the input values, this allows an attacker to send in a malicious payload to the database and pull out any information they can find. 


<hr>

Happy Hacking
![](https://media.giphy.com/media/l3vRmVv5P01I5NDAA/giphy.gif)

### Resources:
1. https://portswigger.net/web-security/sql-injection/union-attacks
2. https://github.com/swisskyrepo/PayloadsAllTheThings/tree/master/SQL%20Injection
3. https://dev.mysql.com/doc/refman/8.0/en/columns-table.html


###### Please Note: that I am still learning and if something that I have stated is incorrect please let me know. I would love to learn more about what I may not understand fully.
