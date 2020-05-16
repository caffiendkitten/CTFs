# Hacker101 CTF - H1 Thermostat

- CTF Name: H1 Thermostat
- Resource: Hacker101 CTF
- Difficulty: Easy
- Number of Flags: 2

Note::: NO, I won't be posting my found FLAGS, but I will be posting the methods I used. 

<hr>

### Flag1
- Hint: 
 1. Doesn't the MAC seem interesting?
 2. Access to the source code would help
 3. Check out the Android Quickstart video from Hacker101
- Acquired By: 
- Oops! I did this one first by accident sooo here it is.
-This flag took a bit as I first had to install an Android Emulator. I chose to use the [Android Studio](https://developer.android.com/studio) and it was straightforward to install but not so straightforward to use. 
-Then it was time to look at the files in the apk through the Android Emulator. I didn't see a way to make this apk run as there was no active "run" button, they were all greyed out. So, now it's time to just look through the files and see if anything is interesting there. As I have not used this/any android emulator before this took a minute to see what my options were and what would populate what. Eventually I found an interesting file called "PayloadRequest". ![Alt Text](https://dev-to-uploads.s3.amazonaws.com/i/ter5sm24hkgaux4e4aqw.png)
-Once I looked at the files I realized that it wasn't straightforward to see what was in each file. ![Alt Text](https://dev-to-uploads.s3.amazonaws.com/i/yhmvofp3vzfmra58dyb8.png) On right-clicking there was an option to "show the bytecode" and this looked like interesting information but this means that I need to look at each file... Not fun but being that this is a CTF I took the chance to just look at them.![Alt Text](https://dev-to-uploads.s3.amazonaws.com/i/ou29ow5cshtcv2t6nyqe.png)
-To my luck there was a flag in the first file. ![Alt Text](https://dev-to-uploads.s3.amazonaws.com/i/uy1l7diod7mhdar41moo.png)
- Learned/Thoughts: 
I learned a bit about Android APKs and that they can hold key information in them that might be want to be kept private. As to what security issue that might be from this... maybe this is an information disclosure issue? This could be an issue if an API key or password is stored in plain text in the apk assuming that users won't have access to it. But, with a simple program it is right there.


### Flag0
- Hint: 
1. Communication is key
2. Have you looked at what the app is sending to the server?
- Acquired By: 
 -I first wanted to see if the key would be in some of the network communications of the downloaded apk file, but there was nothing that I saw that was a flag with Burp or ZAP.![Alt Text](https://dev-to-uploads.s3.amazonaws.com/i/74n5d1gjoxnuai2lz3pl.png)
-From here I looked at the Network tab of the browser and saw that on download there was a Response Header that was not snowing up in Burp. ![Alt Text](https://dev-to-uploads.s3.amazonaws.com/i/n4rstrsj19vwpi07q4ts.png)
-Now it's time to try and decode this Payload. However, Burp didn't like the side of the payload annnnnnd froze.. ![Alt Text](https://dev-to-uploads.s3.amazonaws.com/i/8a3x5og8bzecalam0xns.png)
-Plan B. Because I already have the apk file and the emulator installed I wanted to see what the app would be/doing. As I didn't need to do that for the other flag this means that I need to get the apk file working. This involved deleting it a few times, downloading it again, and instead of simply opening the apk I needed to "profile or debug" and from here I was able to pick a device, edit the configuration, and run it. 
-Oops... No JDK.. Time to install one I guess? ![Alt Text](https://dev-to-uploads.s3.amazonaws.com/i/glm6mhlzdyj8dwli79lp.png) This involved going into my Tools tab, selecting the Android SDK option, going into the SDK Tools tab next to the SDK Platforms tab in the upper middle of the page, and checking off the Android SDK Build-Tools 30-rc1 and letting it run.![Alt Text](https://dev-to-uploads.s3.amazonaws.com/i/f8y8aeht2kwsl1vp1wl1.png)
-Now that that is done I still needed to specify a JDK.. This involves a lot of setup that honestly didn't work for me. Onto Plan C.
-Plan C. This was a process of breaking apart the apk with [Java Decompiler](http://www.javadecompilers.com/) ![Alt Text](https://dev-to-uploads.s3.amazonaws.com/i/tdr3u2oyo8b489z8wrlt.png).
-Once that was broken apart I opened it with Android Studio and was able to look at the "classes.dex" file. Once I was in it I was able to search for a "^flag" which produced me 2 flags. Once that I had found from the previous flag and one for this one.![Alt Text](https://dev-to-uploads.s3.amazonaws.com/i/h26hpvdcszdycwn1jv2r.png)
- Learned/Thoughts: This is another flag that I am not super sure what was learned except that I am able to look at the apk files. I feel like this was not the intended way to solve this flag but it worked... 

### Learned
These CTFs got me to look at Android Studio and apk files. It took me a few days of messing with my computer and programs on it to get this to work so it was more annoying that I feel like I really learned something valuable.

<hr>

Happy Hacking
![](https://media.giphy.com/media/l3vRmVv5P01I5NDAA/giphy.gif)

###### Please Note: that I am still learning and if something that I have stated is incorrect please let me know. I would love to learn more about what I may not understand fully.
