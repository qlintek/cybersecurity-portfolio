## Day 6

### Overheard at Breakfast

#### Brief:
The breakfast terrace is loud this morning, clinking cutlery, espresso machines, the usual chatter. One guest couldn't help but linger at a nearby table, seeing more of a conversation than they were meant to.

When the table's occupants stepped away for a refill, they seized the moment and grabbed a screenshot before it could disappear. Somewhere in that conversation is enough to track down an account nobody was supposed to find. 

#### Itinerary
- [ ] Analyze the provided conversation for identifying details
- [ ] extract the relevant clues
- [ ] locate the hidden account
- [ ] submit the flag

This is the photo I have to analyze:
![alt text](Screenshots/conversation.png)

Of course my eyes immediately gravitated to a tool that let lambo upload their profile and link other media accounts as well that starts with a letter G. and of course their gmail account `lambobytelotushotel@gmail.com`

The category of this room is just OSINT. so I went to google to try to find a tool that starts with `G` and check them for the email address. first one that came up was `Gravatar.com` 

so I then googled `site:gravatar.com lambo byte lotus hotel` and found a gravatar profile for Lambo
![alt text](<Screenshots/Screenshot 2026-08-05 174441.png>)
Which as I highlighted here gives me a hash to decrypt.

I copy and pasted it into crackstation to try to decipher it... no luck
![alt text](<Screenshots/Screenshot 2026-08-05 175152.png>)
thus it is none of the listed hashes. so then I had to ask what looks like a hash but isn't a hash....

after some stressful googling I discovered that hashing always uses hexadecimal `[0-9]` and `[a-f]` this has more letters beyond f and a noob would think is a hash?

BASE64, double confirmed by the fact base64 strings are normally divisible by four (this is 44 characters long) and apparently very popular for CTF events and should have been obvious. while briefly flirting with the idea of writing a python script to decode base64 based off the `Packed Light` room I decided to just continue the trend of open source usage and found a base64 decoding site:
![alt text](<Screenshots/Screenshot 2026-08-05 180053.png>)
and voila there we have the base64 decode and flag acquired.

This room was a humbling lesson about how not every random jumble of scrabble tiles is a hash. But now I know if I see a spilled sack of Bananagrams that include letters of `G` or higher and I am participating in a CTF I should probably not waste my time going to my bookmarks for crackstation and instead spend 20 frustrating minutes trying to get the context correct on a base64 decoding python script and giving up and going to `www.base64decode.org`.