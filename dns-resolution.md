## DNS Resolution

Bob types "example.com" in his browser and presses *Enter*. What happens?

Well, we know that we _intend_ to send a request to the "example.com" server. But one problem we have is servers do not have names like "example.com", or "facebook.com", or "youtube.com" - they have IP addresses. 
"example.com" is just a convenient human-readable alias that gets rid of the need to memorize a bunch of IP addresses. But since ***machines*** talk to each other via their IP addresses, the first thing that 
goes on behind the scenes when Bob presses *Enter* is DNS resolution - the process of finding out the correct IP address for a given hostname. 

### DNS recursor
The DNS recursor is the first hop in the DNS resolution flow. The recursor is the central server that recursively requests information from all other DNS servers to determine the IP address of the host machine.

The first thing the browser does is ask the DNS recursor "hey, what IP address is associated with example.com?". The recursor's job is to hunt down that IP address. The recursor then makes a series of requests to other DNS servers, each of which have different responsibilities. The first request the recursor makes is to the **root nameserver**. 
The root nameserver is the server that contains _mappings of TLDs (e.g. ".com", ".gov", ".org", etc.) to their respective TLD server IP addresses_. 

Think of something like:
```
# TLD server mappings
{
    ".com": "192.168.2.1",
    ".org": "91.18.9.1",
    ".gov": "85.29.4.9",
    ".io": "42.18.2.4",
    etc...
}
```

Each TLD has it's own TLD server. The .com TLD server contains only .com hosts. The .gov TLD server contains only .gov hosts. The .org TLD server contains only .org hosts, etc.


Now, what the heck is a "TLD server"? A TLD server is the next hop in the flow. A TLD server _contains mappings of hostnames to their respective 'authoritative nameservers'_. 
For example, the .com TLD server might contain mappings that look something like:
```
# .com TLD server
# { [hostname] => [authoritative nameserver IP address] }

{
    "example.com": "84.51.13.4",
    "google.com": "29.31.4.1",
    "facebook.com": "64.22.5.7",
    "x.com": "94.3.5.16",
    "instagram.com": "32.45.2.7",
    etc...
}
```

So, what the heck is a "authoritative nameserver"? That's the last hop in the resolution flow. The authoritative name server contains the actual DNS records (e.g. A, CNAME, AAAA, MX, etc.) for a given hostname. This is where we find the IP address (A record) for example.com. This IP address is then returned to the recursor, which then returns the IP to the client / browser.

Here's what it looks like from a high level:

<img width="1150" height="729" alt="image" src="https://github.com/user-attachments/assets/e9606a03-6326-42dd-8496-668de4c08e33" />
