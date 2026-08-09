## Day 2
### Room 404
This room presents me with the information there is a quiet room that is not on the floor plan, is not in the brochure, and does not have a door. Port 8080 is wide open, and the rooms it never lists are worth finding. I am thinking I am going to nmap the ip, and probably use a dirbuster to try to find the hidden directories on that page. and we will see from there.

My first step was trying to Nmap the lab machine I created a directory to save my results to assist in writing this debrief.:

`mkdir nmap`

I then ran:

`nmap -sC -sV -oN nmap/initial <target_ip>`

to try to check what version open services on that ip are and run a few automatic scripts and save it's output to a file named initial

as part of the effects of the flag `-sc` nmap was able to discover an expose git repository on `/.git/`

so on firefox from my browser set attack box I went to 

`http://<target_ip>/.git/`

I also double checked with gobuster I used the command:

`gobuster dir -u http://<target_ip>:8080 -w usr/share/wordlists/SecLists/Discover/WEb-Content/common.txt`

which gave me:

`/.git/head`

I used this information to decide on a tool for dumping the git repository. I used :
`pip install git-dumper`
to install git-dumper that I then called with 
`git-dumper http://<target_ip>:8080/.git loot`
to create a local file named loot of all the contents available on the git repository. 

I searched the loot directory holding the git repo with 

`grep -R "THM" /loot`

I don't know if this is right because I just know the THM flags are ALWAYS 
`thm{something something}`
but it did find the correct flag
