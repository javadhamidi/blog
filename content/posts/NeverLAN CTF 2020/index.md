+++
title = "NeverLAN CTF 2020 Write-up"
author = "Javad Hamidi"
date = "2020-02-08"
description = ""
tags = ["Web", "Misc"]

+++
## Browser Bias

This challenge gives us very little information, just a URL to a site that tells us `Sorry, this site is only optimized for browsers that run on commodo 64`. However, this also narrows our focus down to a singular goal - trying to convince the website that we are accessing it from whatever a 'commodo 64' is.

The first thing we need to know is how the browser can determine what type of client is making a request to it. It turns out, a site can only determine this through the `User-Agent` header. We can manipulate this information in a variety of ways - like with browser extensions or on some browsers using the Developer Tools - but for the sake of controlling exactly what headers are passed over we made this handy little Python function.

```py
import urllib.request

def custom_ua(url, user-agent):
    request = urllib.request.Request(url, headers={'User-Agent': user-agent})
    response = urllib.request.urlopen(request)
    return response.read()
    
custom_ua('https://challenges.neverlanctf.com:1130/', 'our custom header goes here')
```

Next, we have to figure out what our user-agent string should be. If we pass 'commodo 64' as the user-agent parameter of our function nothing changes. Googling commodo 64, we get results for the 1982 computer, The Commodore 64, but making that the header doesn't work either. The part of the challenge we're forgetting is that the site requires `browsers that run on commodo 64`. We need to find the header of a browser that works on the Commodore 64 ...

... here's one! `Hyperlink/2.5e (Commodore 64)`

Put that into our function and we get `Welcome fellow c64 user. flag{8b1t_w3b}`

## Robot Talk

We found the best way to comunicate with [challenges.neverlanctf.com:1120](challenges.neverlanctf.com:1120 "challenges.neverlanctf.com:1120") was through Telnet!

Here's the handy script we made ...

```py
import telnetlib
import base64

tn = telnetlib.Telnet("challenges.neverlanctf.com", "1120")

for i in range(5):
    output = tn.read_until(b"==")
    decode = base64.b64decode(output.split(b": ")[1])
    tn.write(decode)
    print(output, decode)

print(tn.read_until(b"=="))
```

... and here's the outputted flag `flag{Ant1_hum4n}`

## BitsnBytes

Given the 'programming' category, the title, and the fact that we are only linked to an image with green and grey squares, we figured that each colour represented a bit (`1` or `0` in binary) and we had to write a script that would translate it.

```py
file = open("php.svg", "r")
f = file.read().split("/>")
b = ''

for x in f:
    try:
        color = x.split("style='fill:")[1][:7]
    except IndexError:
        continue
        
    if color == "#333136":
        b += "1"
    elif color == "#00ff00":
        b += "0"

import re
b = re.sub("(.{8})", "\\1 ", b, 0, re.DOTALL)

binary_values = b.split(" ")
ascii_string = ""
for binary_value in binary_values:
    an_integer = int(binary_value, 2)
    ascii_character = chr(an_integer)
    ascii_string += ascii_character

print(ascii_string)
```

Here, each green square is decoded as a zero, and each grey is a one.  Since this is an svg, which is conveniently a file type where information is stored in plain text, we didn't need to bring out any Python image-processing libraries. Then we would convert the binary string to plain text.

The problem was that we weren't getting the flag - we were getting something like this `time hash:076a1b24abb4870fc5eb892de796b5b32642f95664c04ff0d9b2b96c58a0b`. The trick to this challenge was that the server only spits out the image of the encoded flag at hourly intervals, during the minute after the clock has shifted to the new hour. Once we got [the correct image](./assets/php.svg) we also got `Now you've got it! Here's your flag: flag{its_all_ab0ut_timing}`.