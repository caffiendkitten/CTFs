# Hacker101 CTF - Micro-CMS v1

MORE CTFSSSSSS
Recently I've started diving into CTFs and trying my hand at some Bug Bounties. This means that I will need to be writing reports with any bug I find and want to practice. So, here I go. 

- CTF Name: Micro-CMS v1
- Resource: Hacker101 CTF
- Difficulty: Easy
- Number of Flags: 4

Note::: NO, I won't be posting my found FLAGS, but I will be posting the methods I used. 

<hr>




### Flag0
- Hint: Try creating a new page
- Acquired By: This one was not super straight forward as the hints it gave (I'm only showing 1) were not straight forward as to what I might need to do. By looking at the page counts I found that, for me, page 6 was "forbidden" and not "Not Found". This means that this is a good starting point... but what to do with it? If you look at how other pages are edited or created we can see there is a naming pattern. So, if we apply that to this "Forbidden" page we can edit it and there is the Flag.
- Thoughts: I actually like that these hints are not straight forward. At first it was a bit annoying but this is showing us how to pay attention and try things within the URL. 

### Flag1
- Hint: Make sure you tamper with every input
- Acquired By: This flag I don't quite understand but I found it by manipulating a "edit" page URL with a single quote (') and then the page that loaded held the flag. 
- Thoughts: The hints lead me to think this would be an alert box but that was not the case. It was more along the lines of path injection as the path was expecting a number but I added a quote to break it.


### Flag2
- Hint: Sometimes a given input will affect more than one page
- Acquired By: This flag I actually got while trying to get Flag1. I found that I could add an alert to the button that was already in one of the pages and when it was clicked an alert popped up. Nothing super special, but when I navigated to another page another alert box populated and there was my flag.
- Thoughts: 


### Flag3
- Hint: Script tags are great, but what other options do you have?
- Acquired By: When you edit the page with the button you can add in a flag value. When you inspect the page the flags value has populated in the code.
- Thoughts: WTF??? I know that I am looking for flags but I wouldn't have thought of adding a flag value to anything to get a flag to populate. This shows an amazing way of taking advantage of values that might be there but not active until a placeholder is available for it.
![Alt Text](https://thepracticaldev.s3.amazonaws.com/i/lr1fp694hm8pgeb6yuby.jpg)


### Learned
I feel like I learned a lot from this set as I am still learning the different ways that pages can be manipulated. I don't feel like the last flag is really something super useful but it is still interesting that this is possible.


Happy Hacking
![](https://media.giphy.com/media/l3vRmVv5P01I5NDAA/giphy.gif)

###### Please Note that I am still learning. If something that I have stated is incorrect please let me know. I would love to learn more about what I may not understand fully.
