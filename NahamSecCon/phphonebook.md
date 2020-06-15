# NahamCon CTF 2020 - Phphonebook

- Resource: NahamCon CTF 2020
- CTF Name: Phphonebook
- Points: 100
- Category: Web

Note::: This CTF is expired and no longer live so flags will be posted.



## Flag1
 __Hint__ 
 1. Ring ring! Need to look up a number? This phonebook has got you covered! But you will only get a flag if it is an emergency! 


 __Acquired By:__
### Step 1. General Look Around
When first looking at this page nothing really seems evident, but there is a note about currently being in `/index.php/?file=` and how there is a phone book on the `phphonebook.php` page. ![Alt Text](https://dev-to-uploads.s3.amazonaws.com/i/h2cv9jxvd4errf863vld.png)

This tells me that there is a second page and the structure of the URL calls, "?file=somethinghere" (might not be the right words but hopefully that makes since.)






### Step 2. How is this information helpful?
If I go to phphonebook.php I can see a picture of a rotary phone and an input field for a phone number. ![Alt Text](https://dev-to-uploads.s3.amazonaws.com/i/rodcp5q9p2h6q0ichmjo.png)
Because of the file structure we can assume that there is some file inclusion and you might be curious to go down that route but I took a different path.

If I add in a number and send it we can see that the network request is changed to a "POST" and there is a parameter for the number that is sent in.![Alt Text](https://dev-to-uploads.s3.amazonaws.com/i/4tr4jnzekzdy0l5ln7u2.png)

 

### Step 3. The Payload
Now that I know there is payload going through I remember that the hint talks about how the flag will only be given if it's an emergency. 
For this I want to change the payload to `emergency=testing` and send it in again. This can be done right in the browser by looking at the Network tab and selecting "Edit and Resend" for the specific request and change the number payload to emergency as follows. ![Alt Text](https://dev-to-uploads.s3.amazonaws.com/i/oj0zbjtlwld6r504d2l1.png)

### Step 4. The Flag
Now that the payload has been sent if I look at the response as a preview or the "response payload" I can see the flag on the page.![Alt Text](https://dev-to-uploads.s3.amazonaws.com/i/26dgpwv43xwptmky9xdd.png)



## Learned
This CTF taught me about hidden payloads that might have meaning but are not explicitly used anywhere as well at parameter tampering.
This attack is a form of Parameter Tampering and if I bring up the page code to look at there are no "hidden" fields that could give away that this form could accept any other type of parameters. ![Alt Text](https://dev-to-uploads.s3.amazonaws.com/i/xtfaj4zkwpru1th4vm26.png)

If we take a step back and remember that this site had a `?file=` parameter and this could be an entry point to Local File Inclusion. By looking at the URL `http://jh2i.com:50002/?file=%22:;//%3E` we can see that we get a blank page pack. Furthermore, any other input that I try also returns a blank page so this could be an entry point but maybe it's not quite formatted right. ![Alt Text](https://dev-to-uploads.s3.amazonaws.com/i/xtfaj4zkwpru1th4vm26.png)

With PHP, in some cases it's possible to run system executables and get Command Execution via PHP Wrappers. PHP wrappers are a number of provided "miscellaneous I/O streams that allow access to PHP's own input and output streams, the standard input, output and error file descriptors, in-memory and disk-backed temporary file streams, and filters that can manipulate other file resources as they are read from and written to." (2)

If I run with this and use the `php://` as people wrappers expect the `//` and apply the `filter` nothing happens. When I tried the URL `http://jh2i.com:50002/?file=php://filter/resource=index.php` the site rather crashed... so that's not quite right.
Going back to the PHP manual on wrappers on link 2 and looking at the "php://filter" section I can see there is a link to a section on accepted filters. There it speaks of 3 filters that are available: String, Conversion, Compression, and Encryption Filters. At first I attempted the string with a rot13 and got a kind of humorous output. As you can see there is clearly some PHP code there in the response but its letters have been rotated by 13 chars. ![Alt Text](https://dev-to-uploads.s3.amazonaws.com/i/yvbgb5ukdhcin2gs5rbt.png)

By trying some other filters I fund the "convert.base64-encode" options and when applied I get back some base64 encoded text. ![Alt Text](https://dev-to-uploads.s3.amazonaws.com/i/1kp7hu8oczc6idg7fy4m.png)
Now take that and decode it and I can see the PHP code that is not available on the client side. ![Alt Text](https://dev-to-uploads.s3.amazonaws.com/i/rzzqd0svo2tpdsu0u9hh.png)

Now that I have a way to view the code I am going to decode the phphonebook.php page and I see there is a "flag.txt" file there IF isset($emergency). ![Alt Text](https://dev-to-uploads.s3.amazonaws.com/i/o5sj7i8ft722kxq853gt.png)
Isset is a function used to determine if a variable is declared and is different than NULL. 
We now verified that this `?file=` is expecting a POST to this form with an "emergency" variable to be set to anything to get the flag.![Alt Text](https://dev-to-uploads.s3.amazonaws.com/i/26dgpwv43xwptmky9xdd.png)




<hr>

Happy Hacking
![](https://media.giphy.com/media/l3vRmVv5P01I5NDAA/giphy.gif)

### Resources
1. https://www.seguetech.com/client-side-scripting-performance/
2. https://www.php.net/manual/en/wrappers.php.php
3. https://www.php.net/manual/en/filters.convert.php
4. https://www.php.net/manual/en/function.isset.php

###### Please Note: that I am still learning and if something that I have stated is incorrect please let me know. I would love to learn more about what I may not understand fully.
