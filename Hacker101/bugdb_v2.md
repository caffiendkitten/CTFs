# Hacker101 CTF - BugDB v2

- CTF Name: BugDB v2
- Resource: Hacker101 CTF
- Difficulty: Easy
- Number of Flags: 1

Note::: NO, I won't be posting my found FLAGS, but I will be posting the methods I used. 

<hr>


### Flag0
- Hint: 
 1. What has changed since last version?
 2. What do the queries tell you?
 3. Have you tried a mutation?
- Acquired By: 
 - This starts out the same a v1 so I first wanted to just poke around again and see if the same queries would work. The final script from the last challenge didn't work but working backwards from the errors I did get some information to show.![Alt Text](https://dev-to-uploads.s3.amazonaws.com/i/k2o25rscw1bqphv5mamv.png) This allowed me to see that there was an "admin" and "victim" user but how to exploit this?
 - Now I wanted to play with the Bugs reports as I am guessing that that is where the flag is again. After some relocating of the "allBugs" query I noticed that it was missing a report from the victim. I bet it is set to private so how do I get it? ![Alt Text](https://dev-to-uploads.s3.amazonaws.com/i/pa0ydgi0s8i22ejo8doh.png)
 - Based on the third hint I need to learn more about GraphQL mutations. Mutations involve its own query where you mutate the Bugs private field. This will involve a mutation function that will change the private field to false and then re-running the previous query to view all the bug texts. ![Alt Text](https://dev-to-uploads.s3.amazonaws.com/i/swk8vhuea7alprrkyhte.png)



### Thoughts/Learned
I learned a bit more about GraphQL and manipulating them but I have a gap as to the practical application of this. I am venturing to guess that because of the nature of GraphQL to give a LOT of information that it is a matter of manipulating the shown image blindly (or based off a known user) to find information that might be there but not directly viewable, yet.


<hr>

Happy Hacking
![](https://media.giphy.com/media/l3vRmVv5P01I5NDAA/giphy.gif)

###### Please Note that I am still learning and if something that I have stated is incorrect please let me know. I would love to learn more about what I may not understand fully.
