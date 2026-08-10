# Splunk Data Pipeline Project
I am trying to learn my way around SPLUNK, primarily because I have seen several cybersecurity analyst job postings mention the software. I started by watching a video series and using copilot to help double check syntax. I now have a simple Splunk pipeline that allows me to SPL query the var logs of an ubuntu server instance I have launched in Virtual Box. 

My first step was to design the infrastructure of the pipeline. I created two Ubuntu Server instance on Virtual Box, Assigned them each 2 CPUs and 8gb of RAM just to be safe, although I know the Forwarder is very lightweight and probably would not need this much RAM. I think found the wget for my Linux OS versions for the Splunk Enterprise and installed it into a server i named "Splunk" and I then unzip the tarball into my opt folder because that is the preferred home for Splunk to live in.

I think went into the /opt/splunk/bin directory and used sudo ./splunk start to launch the Splunk server for my lab. I hit a small snag after I was prompted to create a user account. I am honestly not sure why it happened I was told Splunk always uses the username admin but it would not let me log in.

I had to go through several iterations of downloading, unzipping, deleting, restarting the machine, completely deleting the machine and starting a new one. I even attempted to go into the authentication folders on the server to see if it had anything about the use account I created and it did have the username I created and a password hash yet still i could not log in. Finally, I decided to use a Username other than "admin" and it worked. 

A few minutes after getting the server launched and my new Splunk enterprise account working I went to my host PC and got onto edge to go to `<splunk-machine-ip>:8000`. I went to settings and configured the instance to listen on port 9997. It is at this point I think I technically have a pipeline. It is an indexer and a search head now. but for it to be any use I needed to add a forwarder to put data into the indexer to get parsed. 

That is when I repeated the steps with the Splunk forwarder for Linux and named the other Ubuntu server SplunkFWD
1. get the wget for my Linux version from Splunk for the universal forwarder lightweight application
2. The trick I learned this time around was to sign in to my server using PuTTY so I can copy and paste instead of spending 5 minutes trying to type the wget correctly.
3. I extracted the UF into the opt folder running 
	`sudo tar -xvzf <uf tar file> -C /opt`
4. Then I changed directory into `opt/splunkforwarder/bin` and ran `./splunk start`
(actually I ran /splunk start about seven times trying to figure out while it did not work before realizing what I needed to actually put.)
5. I accepted licensing agreement and initialized UF environment
6. after I got it up and running I ran `./splunk add forward-server <host name or ip address>:<listening port>` to set up forwarding to my receiving enterprise. 
7. I checked enterprise and nothing was coming in yet. I checked if I was forwarding to the server with `/opt/splunkforwarder/bin/splunk list forward-server` and I saw my Splunk enterprise server and for those reading you probably know the obvious step I skipped right over....
8. Then I told Splunk's UF what to monitor: `./splunk add monitor /var/log`
9.  started searching in Splunk for `index=main` and after about 30 seconds I got the data to start rolling in. 
10. I then read Splunk recommends a least privilege user for the Splunk Universal forwarder, I did log into the fool root login shell:
	`sudo -i` - log into root user shell
	`useradd -m splunkfwd` - create a user with a home directory
	`chown -R splunkfwd:splunkfwd /opt/splunkforwader` -adds a user and group as owner of the splunkforwarder file
	``

NEXT STEPS:
now that I have a Splunk environment set up I want to learn more about SPL, making dashboards, and setting alerts. 
I have two projects in mind to implement Splunk:
A. I want to set up a Metasploit able machine, and a Nessus vulnerability scanner and am planning to run the vulnerability scanner on the Metasploitable and using a UF on the Nessus machine to forward to an indexer to be able to build dashboards to identify security flaws in Metasploitable.
B. I want to see if I can run a Valheim game server and Splunk together. I am thinking I saw a UF for docker and my Valheim server runs on docker, that would not be for security but thought it would be a fun project to try to make a dashboard for server uptime, and number of unique player logins, and number of players currently online. just to practice making dashboards with. 
 
	
                 ┌──────────────────────────────┐
                 │   Windows Host PC            │
                 │   192.168.x.X                │
                 │                              │
                 │  Edge → http://splunk:8000   │
                 │  PuTTY → SSH (22)            │
                 └───────────────┬──────────────┘
                                 │
                                 │ SSH (22)
                                 ▼
        ---------------------------------------------------------
        |                 VirtualBox Environment                |
        ---------------------------------------------------------

        ┌──────────────────────────────────────────────────────┐
        │  Splunk Enterprise VM                                │
        │  Hostname: Splunk                                    │
        │  IP: 192.168.x.x                                     │
        │  Role: Indexer + Search Head                         │
        │                                                      │
        │  - Receives logs on port 9997                        │
        │  - Serves Splunk Web on port 8000                    │
        │  - SSH access via PuTTY                              │
        └───────────────▲──────────────────────────────────────┘
                        │
                        │ TCP 9997 (logs forwarded TO Enterprise)
                        │
        ┌──────────────────────────────────────────────────────┐
        │  Splunk Universal Forwarder VM                       │
        │  Hostname: SplunkFWD                                 │
        │  IP: 192.168.x.x                                     │
        │  Role: Log Forwarder                                 │
        │                                                      │
        │  - Monitors /var/log                                 │
        │  - Initiates connection to Enterprise                │
        │  - Sends logs → 192.168.x.x:9997                     │
        │  - SSH access via PuTTY                              │
        └──────────────────────────────────────────────────────┘

