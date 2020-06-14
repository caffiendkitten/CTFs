# NahamCon CTF 2020 - Localghost

- Resource: NahamCon CTF 2020
- CTF Name: Localghost
- Points: 75
- Category: Web

Note::: This CTF is expired and no longer live so flags will be posted.



## Flag1
 __Hint__ 
 1. BooOooOooOOoo! This spooOoOooky client-side cooOoOode sure is scary! What spoOoOoOoky secrets does he have in stooOoOoOore??
 2. Note, this flag is not in the usual format.


 __Acquired By:__
### Step 1. General Look Around
When first looking at this page nothing really seems evident. If you scroll down then more ghosts are added to the page almost endlessly and the script doesn't appear helpful either.![Alt Text](https://dev-to-uploads.s3.amazonaws.com/i/ekacsy5s7lsw435nvwyc.png) 
There is an odd note in the Console Log that I haven't seen before... 
`Intercom 
Array [ "Intercom" ]
 undefined`
![Alt Text](https://dev-to-uploads.s3.amazonaws.com/i/1mqzhutuyebsx62k2bf0.png)
This hint has to do with the first hint about client side code.

Client side code is code that runs in your browser, generally JavaScript, that will be "processed on the client machine and the HTML page will NOT perform a PostBack to the web-server. Traditionally, client-side scripting is used for page navigation, data validation and formatting." (1)






### Step 2. How is this information helpful?
Now that I know that it is dealing with client side code I can dive into looking up information about Intercom and how it works. 

** EDIT ** Turns out this was a rabbit hole.
As I was researching Intercom I started looking at the Javascript code that was being loaded and saw a bunch of Hex code in the jquery.jscroll2.js file. ![Alt Text](https://dev-to-uploads.s3.amazonaws.com/i/j6u4ws1o8bpi0inh9xrx.png)




 

### Step 3. Decode the Hex
Now that I have a bunch of Hex I simply take it all over to a decoder and let it decode it for me.![Alt Text](https://dev-to-uploads.s3.amazonaws.com/i/g4kj6gqsbepn0eb4bfaw.png)




### Step 4. The Flag
Now that the flag is there I can see that it is being loaded in localStoreage by the code just after the flag that is `"setItem","localStorage"`. Now that I know it's in localStorage I am not going to waste any time trying to decode it but instead see what's in the LocalStorage and there it is. 
![Alt Text](https://dev-to-uploads.s3.amazonaws.com/i/anryx3ba3tgbiby598v8.png)




## Learned
This flag showed me some interesting stuff about Intercom but really it taught me to check out some of the more obvious places first. LocalStorage and SessionStorage and fairly common places to store user data.






<hr>

Happy Hacking
![](https://media.giphy.com/media/l3vRmVv5P01I5NDAA/giphy.gif)

### Resources
1. https://www.seguetech.com/client-side-scripting-performance/


###### Please Note: that I am still learning and if something that I have stated is incorrect please let me know. I would love to learn more about what I may not understand fully.
