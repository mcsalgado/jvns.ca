---
title: "How I use Wireshark"
date: 2018-06-19T00:28:24Z
url: /blog/2018/06/19/what-i-use-wireshark-for/
categories: []
---

Hello! I was using Wireshark to debug a networking problem today, and I realized I've never written
a blog post about Wireshark! Wireshark is one of my very favourite networking tools, so let's fix
that :)

Wireshark is a really powerful and complicated tool, but in practice I only know how to do a very
small number of things with it, and those things are really useful! So in this blog post, I'll
explain the 5 main things I use Wireshark for, and hopefully you'll have a slightly clearer idea of
why it's useful.

### what's Wireshark?

[Wireshark](https://www.wireshark.org/) is a graphical network packet analysis tool. 

On Mac, you can download & install it from their homepage, and on Debian-based distros you can
install it with `sudo apt install wireshark`. There's also an official
[wireshark-dev PPA](https://launchpad.net/~wireshark-dev/+archive/ubuntu/stable) you can use to get
more up-to-date Wireshark versions.

Wireshark looks like this, and it can be a little overwhelming at first. There's a slightly
mysterious search box, and a lot of packets, and how do you even use this thing?

<a href="https://jvns.ca/images/wireshark_screenshot.png"><img src="/images/wireshark_screenshot.png"></a>

### Use Wireshark to analyze pcap files

Usually I use Wireshark to debug networking problems in production. My Wireshark workflow
is:

1. Capture packets with tcpdump (typically something like `sudo tcpdump port 443 -w output.pcap`
2. scp the pcap file to my laptop (`scp host:~/output.pcap .`)
3. Open the pcap file in Wireshark (`wireshark output.pcap`)

That's pretty simple! But once you have a pcap file with a bunch of packets on your laptop, what do
you do with it?

### Look at a single TCP connection

Often when I'm debugging something in Wireshark, what's happened is that there's some TCP
connection, and something went wrong with the connection for some reason. Wireshark
makes it really easy to look at the lifetime of a TCP connection and see what happened!

You can do that by right clicking on a packet and clicking "Conversation filter" -> "TCP".

<a href="https://jvns.ca/images/wireshark_filter.png"><img src="/images/wireshark_filter.png"></a>

And then Wireshark will just show you other packets from the same TCP connection as that packet!!
Here you'll see a successful SSL connection  -- there's are packets that say "client hello",
"service hello", "certificate", "server key exchange", which are all part of setting up a SSL
connection. Neat!

<a href="https://jvns.ca/images/wireshark_tcp.png"><img src="/images/wireshark_tcp.png"></a>

I actually used this today to debug an SSL issue -- at work today, some connections were being
reset, and I noticed that after the "client hello" packet was sent, the client was sending a "FIN
ACK" packet which terminates the TLS connection. This was useful because I could tell that the
**client** was terminating the connection, not the server! So immediately I knew that the client was
to blame and I could focus my investigations there.

This pattern is pretty typical of how I use Wireshark. Usually there's a client and a server, and
there's a bug or configuration error on either the client or the server. Wireshark is invaluable for
helping me figure out whether I should blame the client or the server :)

### "Decode as"

Wireshark uses the port to try to guess what kind of packet every packet is, and often it does a
good job! If it sees traffic on port 80, it'll assume it's HTTP traffic, and it's usually right.

But sometimes you have HTTP traffic happening on an unusual port, and you need to give Wireshark
some hints. If you right click on a packet and click "Decode as", you can tell Wireshark what
protocol packets on that port are and then it'll be much easier to navigate and search.

### See the contents of a packet

Wireshark has an AMAZING details view that explains the contents of any packet. Let's take the
"client hello" packet from the details above. This packet is the first packet sent during a SSL
connection -- the client is saying "hello! here I am!".

Wireshark gives you two super useful tools for investigating the contents of a packet. The first one
is this view where you can expand every header the packet has (ethernet header! IP header! TCP
header!) and look at what's in it:

<a href="https://jvns.ca/images/wireshark_packet_details_list.png"><img src="/images/wireshark_packet_details_list.png"></a>

The second view, which is really magical, is this one which shows you the raw bytes of a packet. The
neat thing about this is that if you hover over one of the bytes with your mouse (like here I've
hovered over `tiles.services.mozilla.com`), it'll tell you at the bottom of the screen what field
those bytes correspond to here (in this case the "Server Name" field) and the Wireshark codename for
that field (in this case `ssl.handshake.extensions_server_name`)

<a href="https://jvns.ca/images/wireshark_packet_details.png"><img src="/images/wireshark_packet_details.png"></a>

### Search for specific packets

Wireshark has a great query language, and you can really easily search for specific packets! I
usually just use really simple queries with Wireshark. Here are a few examples of the kinds of
searches I do:

* `frame contains "mozilla"` -- search for the string "mozilla" anywhere in the packet
* `tcp.port == 443` -- tcp port is 443
* `dns.resp.len > 0` -- all DNS responses
* `ip.addr == 52.7.23.87` -- source or dest IP address is 52.7.23.87

Wireshark's packet search language is much more powerful than tcpdump's (and it has tab
completion!!), so I'll often capture a large amount of packets with tcpdump ("all packets on port
443") and then do some more in depth searching using Wireshark.

### see statistics on TCP connection duration

Sometimes I want to specifically investigate slow TCP connections. But what if I have a packet
capture file with many thousands of packets?! How do I find the slow TCP connection?

If you click 'Statistics' in the menu then 'Conversations', Wireshark will give you this amazing
statistics view that looks like this:

<a href="https://jvns.ca/images/wireshark_statistics.png"><img src="/images/wireshark_statistics.png"></a>

This shows me the duration of every single TCP connection, so I can find the long ones and then
investigate them in more detail! So useful :D

### use the latest Wireshark version

If you haven't upgraded Wireshark in a while, it's worth upgrading! I was looking at some HTTP/2
packets on my work laptop recently and was having a tough time. But then I looked at the docs and
realized I was running an old version of Wireshark. I upgraded, and Wireshark's HTTP/2 support had
really improved in the newest version!

### use Wireshark to learn networking protocols

There's some networking jargon in this post ("frame", "tcp port", "dns response", "source IP
address", "SSL client hello"). I left it in because Wireshark definitely doesn't abstract networking
details away from you. That can definitely be intimidating at first!

But Wireshark can actually be a great tool to learn a bit more about networking protocols. For
example, I don't know too much about the details of how the TLS/SSL protocol works! But I can see that
the first two packets are "client hello" and "server hello", and it makes the protocol seem less
like a scary mystery and more like a concrete thing that I can easily look at the details of.

### that's all for now

Wireshark has a TON of features and I definitely only use a small fraction of its features.  The 5
tricks I've described here are probably 95% of what I use Wireshark for -- you only need to know a
little Wireshark to start using it to debug networking issues!
