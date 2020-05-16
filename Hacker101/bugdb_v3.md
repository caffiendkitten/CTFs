# Hacker101 CTF - BugDB v3

- CTF Name: BugDB v3
- Resource: Hacker101 CTF
- Difficulty: Moderate
- Number of Flags: 1

Note::: NO, I won't be posting my found FLAGS, but I will be posting the methods I used. 

<hr>

### Flag0
- Hint: 
 1. What new functionality was added?
 2. Filenames are always interesting
 3. How do you access attachments? Hint: not via GraphQL
- Acquired By: 
 -Based on the previous 2 I was able to see that the "admin" and "victim" were still there but now I had issues finding the bugs text that was previously there. ![Alt Text](https://dev-to-uploads.s3.amazonaws.com/i/3q6e5beub5k0y7yli9uk.png) In fact, the text wasn't there at all.
 -Now that I can see that there is a private setting again and I was able to check if there was an attachment available, but it looks like they were empty. ![Alt Text](https://dev-to-uploads.s3.amazonaws.com/i/8i5gydhma5vmfhfca5gb.png) Lets mutate it with
 `
mutation{
  attachFile(bugId:1, contents:"test"){
    ok
  }
}`
 -This allowed me to potential see an attached file. ![Alt Text](https://dev-to-uploads.s3.amazonaws.com/i/27fddzehrjkb1m8fhc5v.png) What did I try this? The comments mentioned the idea of accessing attachments. ![Alt Text](https://dev-to-uploads.s3.amazonaws.com/i/ap3wrvic71a15gmowugf.png)
 -Now lets decode that Base64 hash with BurpSuite and this shows me the attachment number ![Alt Text](https://dev-to-uploads.s3.amazonaws.com/i/2wrkfcjp1zs26harsffr.png)
 -Now that I have the attachment number I wanted to try running it in the url but that errored out so there must be something else I need to mutate to get access to it.. ![Alt Text](https://dev-to-uploads.s3.amazonaws.com/i/4q3350quv3tk7zsfx2u2.png)
 -For this I just started seeing if anything else populated for the attachment. It was a lot of trial and error but I found a file name section. ![Alt Text](https://dev-to-uploads.s3.amazonaws.com/i/1252jotltu6cwf8o48xo.png)However, it was not an obvious hash to be decoded and seemed to be object data (e33f99ec34e984eb1c7899748e50e928).... I wonder if I can mutate its name like I can with the private setting???
 -Reusing the mutate function from before I modified it to pertain to the userID and its filename for the attachment.
 `
mutation{
  modifyAttachment(id:1, filename:"test.py"){
    ok
  }
}`and it seems to be a success. ![Alt Text](https://dev-to-uploads.s3.amazonaws.com/i/j4gr0lypl7acwuif3724.png)![Alt Text](https://dev-to-uploads.s3.amazonaws.com/i/4n66o8zqjmkev6no3nig.png)
 -Now that the attachment seems to have a new name, lets try it out.
 -ok it failed... I am going to try and rename it a few times to see if maybe a standard page name will pop. I will try things like main, index, and home and see if anything changes.
- I noticed that I was spelling attachment wrong my URL and it doesn't matter what you name the file, so far, and its just a matter of spelling attachment correctly to see the files contents. ![Alt Text](https://dev-to-uploads.s3.amazonaws.com/i/oj2d7wrwn2h279ik9wrr.png)
 -The file appears to be some code for rerouting the attachments and reading the files for FLASK. Are there any other files for Flask that are important? nothing here seems to give any clues except maybe something about the db_sesssion but I don't know what to do with it exactly so its more for more research. 
 -Flask has a few files that are specific to it and those are ![Alt Text](https://dev-to-uploads.s3.amazonaws.com/i/snapcum7ifgdo9yvkoo3.png) And I wonder if I rename the file if different files will populate or if it will stay the same? (This will tell me if my naming it as "test" originally matters.)
 -After renaming the attachment to "model.py", the attachment file changed to show me ![Alt Text](https://dev-to-uploads.s3.amazonaws.com/i/5n1yld51h7ea0iuag5mt.png). Here I see a clue about the database. There appears to be different levels. If I rename the file again will that level show up?
 -I renamed the attachment for the level and a new file showed up with the flag in it. ![Alt Text](https://dev-to-uploads.s3.amazonaws.com/i/gv0oizeqdawrl3uw5bnj.png)



### Learned Overall
This was... Different. I feel like it was a lot about information disclosure as there was a lot of ways to mutate information bits to find more information. I was not super familiar with GraphQL before but now I am definity understanding it better. 



<hr>

Happy Hacking
![](https://media.giphy.com/media/l3vRmVv5P01I5NDAA/giphy.gif)

###### Please Note: that I am still learning and if something that I have stated is incorrect please let me know. I would love to learn more about what I may not understand fully.
