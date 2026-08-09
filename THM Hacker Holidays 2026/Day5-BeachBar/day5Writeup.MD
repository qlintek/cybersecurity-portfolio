## Day 5
###  Beach Bar

#### Briefing

Welcome back to the Byte Lotus — this time the sand is warm, the deck lights are coming up, and the beach bar's jukebox takes requests from anyone with a phone. You spend the evening as a guest at the rail who simply notices things: a DJ who never logs out, a song queue that accepts a little more than song titles, a service down the boardwalk quietly announcing "something".

The beachside guest-experience build shipped on a deadline, and the night-shift developer wired the jukebox straight into the floor with the trimmings still attached.

#### Today's Itinerary
- [ ] Find the use flag
- [ ] Find the root flag


lab machine (target):
`http://10.66.175.64`

The first thing I did was to try to get a feel for what ports and services may be open on the target machine so I ran an Nmap against the target ip.

so I basically copied step one of Room 404 but I created one additional top direcotry
```
mkdir day5
cd day5
mkdir nmap
```
so I made a directory named nmap inside of a directory named day 5.
then I did a network mapper scan of the target ip, saving the results in a file in `day5/nmap`

`nmap -sC -sV -oN nmap/initial 10.66.175.64`

this was the result of that scan:
```
# Nmap 7.94SVN scan initiated Tue Aug  4 22:13:08 2026 as: /usr/bin/nmap -sC -sV -oN nmap/initial 10.66.175.64
Nmap scan report for ip-10-66-175-64.ec2.internal (10.66.175.64)
Host is up (0.00014s latency).
Not shown: 998 closed tcp ports (reset)
PORT   STATE SERVICE VERSION
22/tcp open  ssh     OpenSSH 9.6p1 Ubuntu 3ubuntu13.18 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   256 96:df:2e:51:dd:32:8c:8b:e5:a7:83:e5:33:b4:de:01 (ECDSA)
|_  256 bb:c6:ec:4d:6d:89:b8:4f:9b:eb:88:77:fb:07:5b:be (ED25519)
80/tcp open  http    gunicorn
|_http-server-header: gunicorn
| fingerprint-strings: 
|   FourOhFourRequest: 
|     HTTP/1.0 404 NOT FOUND
|     Server: gunicorn
|     Date: Tue, 04 Aug 2026 22:13:20 GMT
|     Connection: close
|     Content-Type: text/html; charset=utf-8
|     Content-Length: 207
|     <!doctype html>
|     <html lang=en>
|     <title>404 Not Found</title>
|     <h1>Not Found</h1>
|     <p>The requested URL was not found on the server. If you entered the URL manually please check your spelling and try again.</p>
|   GenericLines: 
|     HTTP/1.1 400 Bad Request
|     Connection: close
|     Content-Type: text/html
|     Content-Length: 193
|     <html>
|     <head>
|     <title>Bad Request</title>
|     </head>
|     <body>
|     <h1><p>Bad Request</p></h1>
|     Invalid Request Line &#x27;Invalid HTTP request line: &#x27;&#x27;&#x27;
|     </body>
|     </html>
|   GetRequest: 
|     HTTP/1.0 302 FOUND
|     Server: gunicorn
|     Date: Tue, 04 Aug 2026 22:13:15 GMT
|     Connection: close
|     Content-Type: text/html; charset=utf-8
|     Content-Length: 199
|     Location: /login
|     Vary: Cookie
|     <!doctype html>
|     <html lang=en>
|     <title>Redirecting...</title>
|     <h1>Redirecting...</h1>
|     <p>You should be redirected automatically to the target URL: <a href="/login">/login</a>. If not, click the link.
|   HTTPOptions: 
|     HTTP/1.0 200 OK
|     Server: gunicorn
|     Date: Tue, 04 Aug 2026 22:13:15 GMT
|     Connection: close
|     Content-Type: text/html; charset=utf-8
|     Allow: GET, HEAD, OPTIONS
|     Content-Length: 0
|   RTSPRequest: 
|     HTTP/1.1 400 Bad Request
|     Connection: close
|     Content-Type: text/html
|     Content-Length: 196
|     <html>
|     <head>
|     <title>Bad Request</title>
|     </head>
|     <body>
|     <h1><p>Bad Request</p></h1>
|     Invalid HTTP Version &#x27;Invalid HTTP Version: &#x27;RTSP/1.0&#x27;&#x27;
|     </body>
|_    </html>
| http-title: Beach Bar // Sign in
|_Requested resource was /login
1 service unrecognized despite returning data. If you know the service/version, please submit the following fingerprint at https://nmap.org/cgi-bin/submit.cgi?new-service :
SF-Port80-TCP:V=7.94SVN%I=7%D=8/4%Time=6A7263FB%P=x86_64-pc-linux-gnu%r(Ge
SF:tRequest,184,"HTTP/1\.0\x20302\x20FOUND\r\nServer:\x20gunicorn\r\nDate:
SF:\x20Tue,\x2004\x20Aug\x202026\x2022:13:15\x20GMT\r\nConnection:\x20clos
SF:e\r\nContent-Type:\x20text/html;\x20charset=utf-8\r\nContent-Length:\x2
SF:0199\r\nLocation:\x20/login\r\nVary:\x20Cookie\r\n\r\n<!doctype\x20html
SF:>\n<html\x20lang=en>\n<title>Redirecting\.\.\.</title>\n<h1>Redirecting
SF:\.\.\.</h1>\n<p>You\x20should\x20be\x20redirected\x20automatically\x20t
SF:o\x20the\x20target\x20URL:\x20<a\x20href=\"/login\">/login</a>\.\x20If\
SF:x20not,\x20click\x20the\x20link\.\n")%r(HTTPOptions,B3,"HTTP/1\.0\x2020
SF:0\x20OK\r\nServer:\x20gunicorn\r\nDate:\x20Tue,\x2004\x20Aug\x202026\x2
SF:022:13:15\x20GMT\r\nConnection:\x20close\r\nContent-Type:\x20text/html;
SF:\x20charset=utf-8\r\nAllow:\x20GET,\x20HEAD,\x20OPTIONS\r\nContent-Leng
SF:th:\x200\r\n\r\n")%r(RTSPRequest,121,"HTTP/1\.1\x20400\x20Bad\x20Reques
SF:t\r\nConnection:\x20close\r\nContent-Type:\x20text/html\r\nContent-Leng
SF:th:\x20196\r\n\r\n<html>\n\x20\x20<head>\n\x20\x20\x20\x20<title>Bad\x2
SF:0Request</title>\n\x20\x20</head>\n\x20\x20<body>\n\x20\x20\x20\x20<h1>
SF:<p>Bad\x20Request</p></h1>\n\x20\x20\x20\x20Invalid\x20HTTP\x20Version\
SF:x20&#x27;Invalid\x20HTTP\x20Version:\x20&#x27;RTSP/1\.0&#x27;&#x27;\n\x
SF:20\x20</body>\n</html>\n")%r(FourOhFourRequest,170,"HTTP/1\.0\x20404\x2
SF:0NOT\x20FOUND\r\nServer:\x20gunicorn\r\nDate:\x20Tue,\x2004\x20Aug\x202
SF:026\x2022:13:20\x20GMT\r\nConnection:\x20close\r\nContent-Type:\x20text
SF:/html;\x20charset=utf-8\r\nContent-Length:\x20207\r\n\r\n<!doctype\x20h
SF:tml>\n<html\x20lang=en>\n<title>404\x20Not\x20Found</title>\n<h1>Not\x2
SF:0Found</h1>\n<p>The\x20requested\x20URL\x20was\x20not\x20found\x20on\x2
SF:0the\x20server\.\x20If\x20you\x20entered\x20the\x20URL\x20manually\x20p
SF:lease\x20check\x20your\x20spelling\x20and\x20try\x20again\.</p>\n")%r(G
SF:enericLines,11E,"HTTP/1\.1\x20400\x20Bad\x20Request\r\nConnection:\x20c
SF:lose\r\nContent-Type:\x20text/html\r\nContent-Length:\x20193\r\n\r\n<ht
SF:ml>\n\x20\x20<head>\n\x20\x20\x20\x20<title>Bad\x20Request</title>\n\x2
SF:0\x20</head>\n\x20\x20<body>\n\x20\x20\x20\x20<h1><p>Bad\x20Request</p>
SF:</h1>\n\x20\x20\x20\x20Invalid\x20Request\x20Line\x20&#x27;Invalid\x20H
SF:TTP\x20request\x20line:\x20&#x27;&#x27;&#x27;\n\x20\x20</body>\n</html>
SF:\n");
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
# Nmap done at Tue Aug  4 22:15:15 2026 -- 1 IP address (1 host up) scanned in 127.06 seconds
```
``
nothing immediately stood out for me here so I then went to the website to see what it looked like

`http://10.66.175.64`

brought me to a beach bar log in screen for grins and giggles I tried Admin : Password and got invalid credentials. So then I went into inspect with `ctrl + q` to see if there were any other details I was able to find out. and inside the html script for the website is a comment that they forgot to delete

```
<!--
    staff note: the demo DJ login is still enabled for the soft opening.
    dj / dj  -- swap this before the season starts (ticket BAR-7)
  -->
 
```
  the website allows us to export a yaml playlist and edit it and import it to update the playlist.. I am feeling an input validation or maybe an unrestricted file upload? to get a foothold for Remote Code Execution

![alt text](<Screenshots/Screenshot 2026-08-04 183010.png>)

and the proof is in the pudding I was able to push a yaml file containing python and get some system info about the webserver hosting the dj interface.

So now I need to inject a code to allow RCE.

step 1 is to make sure my attack box is listening. I returned to my terminal window and entered:
`nc -lnvp 4444`

to start a netcat listening on port 4444

next I don't have reverse shell scripts memorized so I used google to find one and chose the first result and hopefully it will work (source: https://pentestmonkey.net/cheat-sheet/shells/reverse-shell-cheat-sheet)



![alt text](<Screenshots/Screenshot 2026-08-04 184356.png>)

![alt text](<Screenshots/Screenshot 2026-08-04 184417.png>)

and yes submitting this allowed for me to find a backdoor into their system and I started rooting around their files to try to find the flag.
I crawled around the directories for the user "bartender for a while and found my way to the correct folder with the flag
![alt text](<Screenshots/Screenshot 2026-08-04 184708.png>)
after getting to the top of this tree by CD .. back and then drilled down into home directory because that is normally where user files are stored and I did indeed find a user.txt that contained a flag.

now when I tried to escalate privileges my screens started to get really wonky. I have never done a privilege escalation before so I did have to find a tutorial that used python to execute the privilege escalation

so as  bartender i ran 
`python3 -c 'import pty;pty.spawn("/bin/bash")'`
and 
then backgrounded the shell and in the normal terminal ran 
`stty raw -echo`
and from there the screen started to get very strange it was like each line was tabbed over and then just kept wrapping but in the next screen is where I found a line about password "SunsetSpritz2024!" by grepping a few random phrases and finding it attached to a line on a jukeboxd.py files --stream-pass SunsetSpritz2024! --bitrate 320k by using the search term "pass"

To test for credential reuse, I tried to run `su` and enter the password when prompted but the screen went haywire. and honestly I just closed the terminal repeated the attack but instead of trying the python bash script again I just did `su` from the normal `nc` window that came up and the password worked. but it did do something weird, my CLI prompt disappeared it was just blank instead of saying `root@10.66.175.64`
but I checked that i was still in the directory and saw that I was, I listed the contents of the directory and cat the root.txt file to get the rest of the data. sorry for the missing screenshots it was from when the screen went a little screwy and I started over without taking a screenshot by accident. 

![alt text](<Screenshots/Screenshot 2026-08-04 191028.png>)

one big take away from this lesson was to be careful about the breadcrumbs you leave around. my initial entry in the system was just some developers throw away comment in the html for the page and then I was able to gain root access because of credential reuse. They gave the password to access the jukebox python script in the clear and that also happened to be the root password as well. I think i will have to seek out more information about privilege escalation because the tutorial I found seemed to give the steps more than give the methodology of the attack type. and it also worked wonky, I know the first time it acted weird was because I missed a single `'` but even after attempting again with proper syntax I was still getting errors. 