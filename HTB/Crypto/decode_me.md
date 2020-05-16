# HTB CTF - Decode Me!!

- CTF Name: Decode Me!!
- Resource: Hack The Box CTF
- Difficulty: [30 pts] easy range

Note::: NO, I won't be posting my found FLAGS, but I will be posting the methods I used. 

<hr>


### Flag1
- Hint:  Try find the flag! 
- Acquired By: 
 - First thing to do is obviously download the file and extract the file. While a lot of people will use the command line for this I usually just to my file folders. It's easy so why not.
 - Next is to just look at it.![Alt Text](https://dev-to-uploads.s3.amazonaws.com/i/k3jzwuxrxt8lsa7mq8u6.png) At first it appears to be something that is Base64 encoded as the lines end with the usual "=" but this is not the case... yet. So first thing I did, like many others, is try to decode it like this, but it fails.
 - After a bunch of Googling and browsing the HTB fourms someone mentions a key and code so if we look into that we find the Fernet (symmetric encryption). This is a "symmetric encryption method which makes sure that the message encrypted cannot be manipulated/read without the key. It uses URL safe encoding for the keys. Fernet uses 128-bit AES in CBC mode and PKCS7 padding, with HMAC using SHA256 for authentication. The IV is created from os.random()." (1)
 -  This produced something that was less than finished but it looks like there is another string of Base64 so lets decode that now.![Alt Text](https://dev-to-uploads.s3.amazonaws.com/i/6lambme1xisjfjmc2cna.png)This is still not really helpful though...
 - Now it's time for MORE Googling and HTB forums and I got a hint about "Malbolge Tools". Malbolge is "Malbolge is a public domain esoteric programming language... that was specifically designed to be almost impossible to use, via a counter-intuitive 'crazy operation', base-three arithmetic, and self-altering code." (2)
 - Once I found the Malbolge Decoding tool I was able to throw in the new output and get the flag.![Alt Text](https://dev-to-uploads.s3.amazonaws.com/i/jhxpmufnf32sfw7qhaxd.png)


### Learned
This flag taught me the power of forums and getting help.
This flag was a crazy 3 step process that I would not have been able to get on my own. The input for the Malbolge Tool was something I have never seen before and don't think that I will see again outside of CTFs but I am glad I've seen it.

<hr>

Happy Hacking
![](https://media.giphy.com/media/l3vRmVv5P01I5NDAA/giphy.gif)

### Resources:
1. https://asecuritysite.com/encryption/ferdecode
2. https://en.wikipedia.org/wiki/Malbolge
3. https://zb3.me/malbolge-tools/
4. https://asecuritysite.com/encryption/ferdecode


###### Please Note: that I am still learning and if something that I have stated is incorrect please let me know. I would love to learn more about what I may not understand fully.
