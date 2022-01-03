# HTB CTF - Fuzzy

- CTF Name: Fuzzy
- Resource: Hack The Box CTF
- Difficulty: [20 pts] range
- Number of Flags: 1
- Task: Obtain Admin password

Note::: NO, I won't be posting my found FLAGS, but I will be posting the methods I used. 

<hr>


### Flag1
__Acquired By:__
1. Install "go buster" (GB) if you don't already have it.
2. Run GB to get a list fuzz a list of folders on the site with `gobuster dir -u http://docker.hackthebox.eu:XXXX/ -w /usr/shrea/dirbuster/wordlist/directory-list-2.3-medium.txt`
   Response: Returns 3 folders of "/css", "/js", and "/api".
3. Navigate to each one and note that the "/api" is the only valid page response while both "/css" and "/js" return both blocked responses.
4. Run GB again with the aggitional flag of "-x php,txt" to see if it got anything else out of the api folder.
5. Navigate to the restuned page and note the "Error: paamater not set" response.
6. In a porxy, resend the request for the found page and fuzz it for common paramater names and paramater values to get the ....

7.0 NOT FINISHEDDDDD




### Learned
This challenge taught me a little bit about WFUZZ and the practice fuzzing multiple input points to get different results.

<hr>

Happy Hacking
![](https://media.giphy.com/media/l3vRmVv5P01I5NDAA/giphy.gif)

### Resources:
1. https://robertheaton.com/2013/07/29/padding-oracle-attack/


###### Please Note: that I am still learning and if something that I have stated is incorrect please let me know. I would love to learn more about what I may not understand fully.