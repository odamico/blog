---
title: "ARP poisoning using Kali Linux"
author: "Oscar D'amico"
date: 2020-01-19
draft: false
tags:
  - ARP
  - Kali-Linux
  - security

---

One of the fundamental protocols in the TCP/IP stack is the address resolution protocol, or ARP for short. 
This protocol maps IPv4 addresses to MAC addresses but performs no authentication by design. 
Because of this, we can send our own ARP traffic onto the network claiming to own an IP address which we really don't. 
In this post, I will be using this to my advantage in an ARP spoofing attack. First, let's look at my networking setup. 

{{< figure src="https://odamico.github.io/setup.png" alt="Lab Network Diagram" caption="Lab Network Diagram" >}}

My kali attacker will be forging arp packets claiming to be the ISP router and victim machine. This will allow the attacker to listen
in on communications between the router and the victim (and later on, to alter those communications).

Firstly, lets open up ettercap using the following command on our attacker
```sh
$ ettercap -G
```
Once it's open, we select our bridged interface and click the "accept" button:

{{< figure src="https://odamico.github.io/ettercap-G.png" alt="Ettercap interface" caption="Ettercap GUI" >}}

Then, we can perform host discovery by clicking the "scan" button as seen below:

{{< figure src="https://odamico.github.io/scan.png" alt="Ettercap scan button" caption="Host discovery on 192.168.17.0/24" >}}

Since we know the hosts at 192.168.17.1 and 192.168.17.26 are our targets from our network diagram, we can just click to add
the .1 entry to target 2 and the .26 entry to target 1. We should see these messages in the ettercap console to confirm:

{{< figure src="https://odamico.github.io/ettercapConsole.png" alt="Ettercap console interface" caption="Ettercap text console" >}}

With our targets in place, we select "MITM Menu" --> "ARP Poisoning..." --> "Ok" to start sending out our ARP packets. 
The confirmation can be seen in the console once again:

{{< figure src="https://odamico.github.io/Victims.png" alt="Ettercap's confirmation of victim groups being poisoned" caption="Ettercap text console confirming our victims are being poisoned" >}}

Why don't we perform a little bit of traffic analysis to see what ettercap is actually doing behind the scenes?
Below is a tcpdump capture of the arp traffic that the attacker's network interface is seeing. 

{{< figure src="https://odamico.github.io/tcpdumpARP.png" alt="tcpdump on kali linux capturing ARP packets on eth0" caption="tcpdump ARP packet capture" >}}

From the tcpdump output, we can see that the attacker responds to several ARP requests. The third ARP packet shows the attacker replying, 
saying that it is the owner of 192.168.17.25. This is true, that's our DHCP-assigned address. However, we can see that the attacker
is also replying to queries for 192.168.17.26 and 192.168.17.1. The attacker is claiming that its MAC address of 00:0c:29:57:e9:0d 
corresponds to both of those IP addresses in ARP packet numbers 6 and 7 of our capture. I included the ip command output at the bottom
to show that that really is our attacker's MAC address. 

Next, let's check on our victim to see if our attack worked. To do this, I will ping the gateway from the victim and see the 
results in our attacker's wireshark packet capture. 

{{< figure src="https://odamico.github.io/arpPing.png" alt="Ping from windows xp victim" caption="Ping to gateway from windows xp victim" >}}

{{< figure src="https://odamico.github.io/sharkICMP.png" alt="wireshark on kali linux capturing ICMP packets between the gateway and victim" caption="wireshark ICMP packet capture" >}}

Not only can we see icmp, we can also see http traffic as shown below:

{{< figure src="https://odamico.github.io/httpCapture.png" alt="Eavesdropping on the vicitm's web traffic" caption="HTTP traffic in wireshark" >}}

We've succesfully performed a basic ARP spoofing attack. In the future, I'd
like to expand on this attack and get into spoofing some services, so stay 
tuned for a part 2!
