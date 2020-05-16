# HTB CTF - I know Mag1k

- CTF Name: I know Mag1k
- Resource: Hack The Box CTF
- Difficulty: [50 pts] medium range

Note::: NO, I won't be posting my found FLAGS, but I will be posting the methods I used. 

<hr>


### Flag1
- Hint: Can you get to the profile page of the admin?  
- Acquired By: 
 1. When first looking at the site it appears to be a simple login page.![Alt Text](https://dev-to-uploads.s3.amazonaws.com/i/8quwwcw1ikcuotvo7abi.png) If I look at the code there is nothing special is the code itself or the cookies.
 2. Now I want to look at the network traffic, and again, nothing stands out to me. ![Alt Text](https://dev-to-uploads.s3.amazonaws.com/i/vaf9pf5vgg6ubaqavt39.png)
 3. Now I want to register and see if it will let me register. Again, there is nothing special in the code, network, or storage of the site currently. First thing I do it attempt to login at admin but it tells me my username/password is wrong. So I try registering as admin and it lets me. Interesting. 
 4. Now that I have an account with a username of "admin" and I am logged in I see that I have a new cookie.![Alt Text](https://dev-to-uploads.s3.amazonaws.com/i/hljnyebebc6ynv3xg0jt.png) This cookie is obviously URL encoded and base64. This is obvious because of the "%3D" and that there are two of those at the end of the cookie. "%3D" becomes an "=" and having two at the end of a string is common base64 encoding so I want to try and decode it. For this I will be using BurpSuite's Decoder because I like that it lets me chain decoding easily. Sadly, this did not produce anything obvious. ![Alt Text](https://dev-to-uploads.s3.amazonaws.com/i/rvbxkei24s9fylgtxcs8.png)
 5. This CTF is talking about magic and after some research into encryption padding I see there is a thing called "Oracle Padding Attack" and I believe this is it. The oracle padding attack utilizes the blocks of each bytes to decrypt the results of encryption and I need to work with padBuster to solve this. To use padBuster I will run `perl padBuster.pl http://docker.hackthebox.eu:xxxxx/profile.php F%2BRHLTZ80qRhaTpGGnvdimT0Dob9Qrgk4IkCNgBQYgDlmEc99emZPA%3D%3D 8 --cookies iknowmag1k=F%2BRHLTZ80qRhaTpGGnvdimT0Dob9Qrgk4IkCNgBQYgDlmEc99emZPA%3D%3D` to have it sycle over it and break it. ![Alt Text](https://dev-to-uploads.s3.amazonaws.com/i/3eztna96ulwia2rmoyoh.png) Once it completed I got an output that revealed the formatting of the cookies data. ![Alt Text](https://dev-to-uploads.s3.amazonaws.com/i/j7o77ef4xe62jhlwqff0.png)
 6. Now that I know the layout I can see that I have a role that I need to change to "admin". To do this we will use padBuster again and add in the plaintext of the formatting of the new cookie we want. `perl padbuster.pl http://docker.hackthebox.eu:30769/profile.php F%2BRHLTZ80qRhaTpGGnvdimT0Dob9Qrgk4IkCNgBQYgDlmEc99emZPA%3D%3D 8 --cookie "iknowmag1k=F%2BRHLTZ80qRhaTpGGnvdimT0Dob9Qrgk4IkCNgBQYgDlmEc99emZPA%3D%3D" -plaintext "{\"user\":\"admin\",\"role\":\"admin\"}"` This produced a new cookie with me as the role "admin". ![Alt Text](https://dev-to-uploads.s3.amazonaws.com/i/ow5rcw3pyibojpr0g5c6.png)
 7. Now that I have a new cookie I am able to copy it and paste it into the browser and now when I refresh the page I am an admin and the flag is right there.![Alt Text](https://dev-to-uploads.s3.amazonaws.com/i/zf7t1s1vfmsg7zdq7wn0.png)


### Learned
This CTF taught me about the padBuster tool the most.
I am using this as a shooting off point to learn more about padding and cipher block chaining that I will write about later. 

<hr>

Happy Hacking
![](https://media.giphy.com/media/l3vRmVv5P01I5NDAA/giphy.gif)

### Resources:
1. https://robertheaton.com/2013/07/29/padding-oracle-attack/


###### Please Note: that I am still learning and if something that I have stated is incorrect please let me know. I would love to learn more about what I may not understand fully.