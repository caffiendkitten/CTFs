- CTF Name: Hello World!
- Resource: Hacker101 CTF
- Difficulty: Moderate
- Number of Flags: 1

Note::: NO, I won't be posting my found FLAGS, but I will be posting the methods I used. 

<hr>

### Flag0
__Hint__ 
 1. What does the application do?
 2. Where does your input go?
 3. Look for interesting functions


__Acquired By:__
 1. Like other flags the first thing I want to do is look at the code. However, this application is very simple. ![Alt Text](https://dev-to-uploads.s3.amazonaws.com/i/sx4rop3yrplkd3p9e7mk.png)![Alt Text](https://dev-to-uploads.s3.amazonaws.com/i/20shpy4pjmkpzc6xygq7.png)It has a file to download and an input point. 
 2. Now if we try and use this input with regular words we can see that anything we input is displayed back on the page AND it is also in the URL now. ![Alt Text](https://dev-to-uploads.s3.amazonaws.com/i/fgva7sdo8uqlktbmrsk6.png)
![Alt Text](https://dev-to-uploads.s3.amazonaws.com/i/hs3evzdqkmy18f4ubhg5.png)
 3. Taking a step sideways let's look at the file real quick.![Alt Text](https://dev-to-uploads.s3.amazonaws.com/i/3lr27hw8emnczfxfh0vr.png) It appears to be something executable based on that `/lib64/`. This also tells us some useful information, this uses a 64 bit architecture.
 4. There doesn't currently seem to be anything else useful so let's take this 64 bit architecture information and try some inputs in the "Stdin" field. If we populate some of the general inputs we can see that it is encoding a "#" mark and leaving everything else in the URL. I'm going to guess that we need some encoding then. ![Alt Text](https://dev-to-uploads.s3.amazonaws.com/i/12o8wcll5s0q7qxy0db0.png)
 5. Now if we swing back to the idea of a 64 bit architecture... "in computer architecture, 64-bit integers, memory addresses, or other data units are those that are 64 bits (8 octets) wide." (1) So, I am going to use [this byte counter](https://mothereff.in/byte-counter) to help me create a payload (because I don't remember how many characters are needed for this). I am going to use only "A"s at first and this returns a "core dump" ![Alt Text](https://dev-to-uploads.s3.amazonaws.com/i/8cvv42x7alt9xliwdszb.png). 
 6. It need a specific length for the file so I am going to try and add null bytes to the end one at a time in place of an "A" and see if or when it changes. However, now matter how many I placed it still didn't populate the flag.
 7. After some more research into this I learned about the file type and how binary exploitation works, see "learned" section below for more info, I now need to change the exploit. Let's explore this a bit more.
  - If we look at the file with `file vulnerable` we can see that it is an "ELF" file. 
  - If we use the `readelf -h vulnerable` command, we can see that it has a header size of 64 bytes and is a UNIX system with 9 program headers. ![Alt Text](https://dev-to-uploads.s3.amazonaws.com/i/7lqkci3ogylt1psql4n9.png)
  - Now we need to run `readelf -l vulnerable` to see the list of the program headers and we will notice that there is a specific formatting to the address, such as "0x0000000000400040". ![Alt Text](https://dev-to-uploads.s3.amazonaws.com/i/wr9b5eoeyewmxqxoarot.png)
  - By running the command `readelf --symbols vulnerable` I can now see that the "Symbol table '.symtab' contains 71 entries" and one of them is "print_flags" with a value of "00000000004006ee". ![Alt Text](https://dev-to-uploads.s3.amazonaws.com/i/y5zcorlhhviuj43im8zn.png)
  - If we try this in this formatting the link with this as `AAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAA%00%00%00%00%00%40%06%ee`, it still fails. 
  - I wanted to try and see inside the function but the closest I could get was this with `objdump -d vulnerable`.![Alt Text](https://dev-to-uploads.s3.amazonaws.com/i/9iqgijqmq2vl5ydlnlj7.png)
8. After some trial and research I decided to send it backwards as `AAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAA%ee%06%40%00%00%00%00%00`. Once you send this through, either in the URL or directly in the input box, the flab populates. ![Alt Text](https://dev-to-uploads.s3.amazonaws.com/i/yg8czht0nuz9tb1ho0tp.png)



#### Learned
This flag showed me about C language library and binary exploitation. 

"In computer architecture, 64-bit integers, memory addresses, or other data units are those that are 64 bits (8 octets) wide." (2)

When you look up some bits on readable strings in the "vulnerable" document, `libc.so.6exitputsstdinprintffgetcmemsetgetenv__libc_start_main__gmon_start__GLIBC`, you can find information the C library and binary exploitation.
 - "The _libcstart_main() function shall perform any necessary initialization of the execution environment, call the main function with appropriate arguments, and handle the return from main(). If the main() function returns, the return value shall be passed to the exit() function."(3)
 - "gmon_start
The function call_gmon_start initializes the gmon profiling system. This system is enabled when binaries are compiled with the -pg flag, and creates output for use with gprof(1). In the case of the scenario binary call_gmon_start is situated directly proceeding that _start function. The call_gmon_start function finds the last entry in the Global Offset Table (also known as __gmon_start__) and, if not NULL, will pass control to the specified address. The __gmon_start__ element points to the gmon initialization function, which starts the recording of profiling information and registers a cleanup function with atexit(). In our case however gmon is not in use, and as such __gmon_start__ is NULL." (3)


<hr>

Happy Hacking
![](https://media.giphy.com/media/l3vRmVv5P01I5NDAA/giphy.gif)

### Resources
1. https://en.wikipedia.org/wiki/64-bit_computing
2. https://mothereff.in/byte-counter
3. https://bitvijays.github.io/LFC-BinaryExploitation.html

###### Please Note: that I am still learning and if something that I have stated is incorrect please let me know. I would love to learn more about what I may not understand fully.
