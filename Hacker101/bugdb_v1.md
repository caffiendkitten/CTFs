# Hacker101 CTF - BugDB v1

- CTF Name: BugDB v1
- Resource: Hacker101 CTF
- Difficulty: Easy
- Number of Flags: 1

Note::: NO, I won't be posting my found FLAGS, but I will be posting the methods I used. 

<hr>




### Flag0
- Hint: 
 1. What can you see? What can you not see?
 2. What data types are involved?
 3. Have you tried querying different endpoints?
- Acquired By: 
 - This was a big odd of a hint so I started out just looking at the super blank page and looking at its code.![Alt Text](https://dev-to-uploads.s3.amazonaws.com/i/86pjkgy95f8iux42vsow.png)
 - Now that I've looked over it all I will poke around at the GraphQL tool that it is. ![Alt Text](https://dev-to-uploads.s3.amazonaws.com/i/xz3anna20wem39q8zgax.png)
 - With this CTF the left side of the page is the query and the right side is the output. This required knowing what queries GraphQL will accept soooo let's try some things. ![Alt Text](https://dev-to-uploads.s3.amazonaws.com/i/tk5otik2qua36jit11ta.png)![Alt Text](https://dev-to-uploads.s3.amazonaws.com/i/4tktp35w4m1tkpwgcay1.png)
 - Now I need to find the specific pattern to get alllllll of the information. Time to try some more things![Alt Text](https://dev-to-uploads.s3.amazonaws.com/i/1ptl0q8o76iya0e2f4hn.png)
 - After some research and adding lots of things to the query I was able to get all the information to show up. ![Alt Text](https://dev-to-uploads.s3.amazonaws.com/i/qm5kt1fivn282tel29a5.png)If you look in the bugs of the second node ID there is a "text" field that has the flag.![Alt Text](https://dev-to-uploads.s3.amazonaws.com/i/brg53dgsfauzdj1ta17g.png)



### Thoughts/Learned
I have not played around with graphQL much so this was an interesting CTF to get me to research it more. I am looking forward to the second on and seeing how this will change.
I don't _really_ know what I learned outside of more about GraphQL as this environment was set up for this. 


<hr>

Happy Hacking
![](https://media.giphy.com/media/l3vRmVv5P01I5NDAA/giphy.gif)

###### Please Note that I am still learning and if something that I have stated is incorrect please let me know. I would love to learn more about what I may not understand fully.
