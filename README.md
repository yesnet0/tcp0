# tcp0
Research into publicly addressable tcp:0 

> tl;dr: 0.06% of the publicly-addressable IPv4 space is listening to and responding on TCP Port 0. Why? idk...

Note: Never interact with a computer system beyond your level of authorization (i.e. without getting permission first). 

It started with this tweet:

https://twitter.com/tirtha_mandal/status/1285497244636995584?s=20
> Always check for open ports on port 1-65535. Last night got an admin panel running on port 52233 & found the default creds from the docs.(Pwn3d!!) #bugbounty

It's a great tip, especially for newer bounty hunters and hackers who've followed what I refer to as the "outside-in offense" pathway and learned a bunch about web apps, before understanding networking fundamentals like port assignments and the OSI stack. 

Of course, given this was retweeted in the afternoon in Australia, #scotcherati infosec networking mirth ensued:

https://twitter.com/wan0net/status/1285823486250737664?s=20
https://twitter.com/caseyjohnellis/status/1285824269130194944?s=20

Which got me thinking... 
* TCP:0  is a [real port](https://community.cisco.com/t5/network-security/tcp-port-0/td-p/693936), and
* historically has been flagged by vulnerability scanners as [Suspicious](https://www.tenable.com/plugins/nessus/18164), but 
* is meant for [internal processes](https://www.lifewire.com/port-0-in-tcp-and-udp-818145#:~:text=Port%200%20is%20a%20wildcard,number%20zero%20up%20to%2065535.), and shouldn't be bound to 0.0.0.0 in a manner that allows an external TCP handshake to complete - If at all).
* Additionally, while I've bumped into it on internal networks from time to time over the year, I've usually seen it packet filtered via anything that is doing NAT (e.g. an edge router or firewall).

In short, something is weird if you can do a handshake with TCP:0 across the internet with a publicly addressable. Of course, that beggared the question "how weird is the internet, exactly" and, thanks to [zmap](https://zmap.io/) I was able to find out.

Zmap is a very, very fast TCP connection testing tool which allows Internet-scale scanner for TCP and UDP ports. On a single DigitalOcean droplet a scan of the entire IPv4 space available to that machine (3,702,258,432 IP addresses) yielded 2,151,282 hosts that responded to a full TCP SYN/ACK handshake. 

> 19:14:10 100% (1s left); send: 3702258432 done (53.5 Kp/s avg); recv: 2151282 0 p/s (31 p/s avg); drops: 0 p/s (0 p/s avg); hitrate: 0.06%
> Jul 30 03:43:28.489 [INFO] zmap: completed

So... does anyone know what the heck is going on here? Drop an Issue or push a PR with any discoveries, theories, or findings. Perhaps there's a simple explanation that I've missed when researching this... or perhaps the new #bugbountytip is "Always check for open ports on port 0-65535."

The full list from 29 July 2020 can be accessed in gzip format [here](https://m.cje.io/2BLC4xo). 

I'm not sure of the root cause and I'm not able to dig into full-time at the moment, but *I DO KNOW* that basically no-one is looking at this port at across the Internet, and in a bug bounty context. If I was a bug hunter, I'd see this as an opportunity to move first... and I would:
1. Use something like the [disclose.io list](https://github.com/disclose/disclose/blob/master/program-list/program-list.json) of VDP and bounty programs or Arkadiy's [bug bounty platform scope scraper](https://twitter.com/arkadiyt/status/1287482328437137410?s=20) to assemble a list of targets which are a) authorized for testing, and b) will get you paid.
2. Cross-reference the list of IPs from the first step against the [most recent scan of TCP:0](https://m.cje.io/2BLC4xo) to create a condensed, approved for testing list of targets that respond to TCP:0.
3. Feed the hosts from step 2 into your infrastructure attack tool chain, and approach the open port as you would on any other typical host.
4. ???
5. PROFIT (...and solve a mystery and make the Internet a little safer).

Please do share what you find as apporpriate, and any explanations for why this is happening in the first place would be AMAZING.

---
