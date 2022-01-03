# HTB CTF - Emdee Five For Life

- CTF Name: Emdee Five For Life
- Resource: Hack The Box CTF
- Difficulty: [20 pts] range
- Number of Flags: 2
- Task: encrypt MD5 hash fast enough to get flag

Note::: NO, I won't be posting my found FLAGS, but I will be posting the methods I used. 

<hr>


### Flag1
__Acquired By:__
1. After intercepting the page source and trying to encrypt the string it is apparent that something else needs to be done. Entryption needs to happen on load and faster. To do so I built the following python3 script:
```import requests
import hashlib
import re

url-"http://docker.hacktheboc.eu:XXXXX/"
r = requests.session()
out = r.get(url)
out = re.search("<h3 align='center'>+.*?</h3>", out[0])
out = re.search("'>,*<",out[0])
out = re.search("^|'|>}<]", out[0])
out = hashlib.md5(out[0].encode('utf-8')).hexdigest()
print("sending md5:-{}".format(out))
data={"hash":out}
out = r.post(url=url, data=data)
print(out.text)
```
2. Run the script and the flag appears in the outpout of the h3 tag.


### Flag2
1. with WFUZZER to test for the param run `wfuzz --hh=24 -c -w /usr/share/dirb/wordlist/big.txt http://docker.hackthebox.eu:XXXXX/api/action.php?FUZZ=test`
    - Response: 1 payload responsed with 1 word: "XXXXXXXXXXXX"
2. Place the received word in the url in place of the "FUZZ" and run WFUZZ again for "test". 
    - Response: There are a lot of responses but one of them was successful and contains the flag.


### Learned
This challenge taught me a little bit about WFUZZ and the practice fuzzing multiple input points to get different results.

<hr>

Happy Hacking
![](https://media.giphy.com/media/l3vRmVv5P01I5NDAA/giphy.gif)

### Resources:
1. https://robertheaton.com/2013/07/29/padding-oracle-attack/


###### Please Note: that I am still learning and if something that I have stated is incorrect please let me know. I would love to learn more about what I may not understand fully.