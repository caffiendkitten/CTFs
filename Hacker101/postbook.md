# Hacker101 CTF - Postbook

Recently I've started diving into CTFs and trying my hand at some Bug Bounties. This means that I will need to be writing reports with any bug I find and want to practice. So, here I go. 

- CTF Name: Postbook
- Resource: Hacker101 CTF
- Difficulty: Easy
- Number of Flags: 7

Note::: NO, I won't be posting my found FLAGS, but I will be posting the methods I used. 

<hr>


### Flag0
- Goal: The person with username "user" has a very easy password...
- Acquired By: username:user and password:password.
- Thoughts: This is an easy one, obviously and when I first tried it I tried user for the password and failed. My second choice was 'password' and that worked and then Boom. flag.

### Flag1
- Goal: Try viewing your own post and then see if you can change the ID
- Acquired By: Navigating to a URL with a different 'id=#" than my own.  navigate to a URL for a post by id: `http://....../index.php?page=profile.php&**id=d**`
- Thoughts: This is something that every site should be preventing. This is an easy way to try and jump around when it is seen that the URL is a path to a specific thing and it can be manipulated. The URL manipulation should be protected against and specific things should not be allowed to be input into the URL, such as `<script>` tags or really anything that is a path that is not desired.

### Flag2
- Goal: You should definitely use "Inspect Element" on the form when creating a new post
- Acquired By: Changing a hidden field in a form lead to the capture of this flag. By changing the user_id's value this allowed me to post as someone else.
![Alt Text](https://thepracticaldev.s3.amazonaws.com/i/igpktb22rqms0scajp52.jpg)
- Thoughts: Though 'hidden' fields are  used sometimes this can be a giveaway as to what information might be able to be changed or leveraged to do unexpected things. If at all possible, don't have hidden fields.

### Flag3
- Goal: 189 * 5
- Acquired By: 189 * 5 = 945... This is the ID of a mystery post. Change the post ID like before to 945 and find your flag.
Thoughts: It is a common tactic to number posts like this but it might be a mistake if a post is 'private' and can be viewed by simply changing the id number.

### Flag4
- Goal: You can edit your own posts, what about someone else's?
- Acquired By: Like the 'hidden' field before I tried changing the id of the edit button. ![Alt Text](https://thepracticaldev.s3.amazonaws.com/i/4vlly0jmkyd36h21kwd7.jpg)

### Flag5
- Goal: The cookie allows you to stay signed in. Can you figure out how they work so you can sign in to user with ID 1?
- Acquired By: When you inspect the page on login you can see the cookie id=someMD5HashAgain where the hash is the user id. If we use an addon that allows us to edit the cookie we can get the MD5 hash for the number 1 and replace the cookie with the new hash. On refresh the site will go off of the new cookie set and log in a user 1.
![Alt Text](https://thepracticaldev.s3.amazonaws.com/i/lxovuh3qp54wgfb3q4kc.jpg)

### Flag6
- Goal: Deleting a post seems to take an ID that is not a number. Can you figure out what it is?
- Acquired By: Like Flag4 I needed to change the id of the delete button and that allowed the flag to be acquired. This one was a big difficult though as the id was a hash. MD5 to be specific. The thing with MD5 hashs is that they always produce the same output so by decoding it and then picking the hash for a different user is simple.
![Alt Text](https://thepracticaldev.s3.amazonaws.com/i/w89bghqrp77r82h9uo33.jpg)


### Learned
I learned a lot about manipulating cookies and maneuvering between user accounts here. Granted, this was an east level and a real site should protect against this tactics but that is not always the case. There are a lot of new developers in the world not being taught to protect against these things so they can still sneak into new projects and should be kept an eye out for.

Happy Hacking
![](https://media.giphy.com/media/l3vRmVv5P01I5NDAA/giphy.gif)

###### Please Note that I am still learning. If something that I have stated is incorrect please let me know. I would love to learn more about what I may not understand fully.
