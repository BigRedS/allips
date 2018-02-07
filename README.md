# allips

Pure-perl, no-dependencies way of getting all the configured IP addresses 
on a linux system.
Annoyingly, the ipv6 subsystem has a really easy way to answer this question
(`/proc/net/if_inet6`) but there's no ipv4 analogue.


## Sources ##

* `/proc/net/udp` & `/proc/net/tcp`

which contain details about *currently open* sockets. So they'll list any 
addresses that are sending or receiving packets, but not completely dormant
ones. Apparently, this is about as reliable as it gets :/

(disabled by passing '--noudp' and/or '--notcp' as arguments, respectively)

* `ifconfig`

which is, essentially the same as above but via ioctl rather than parsing
ASCII. The output of the ioctl is, of course, rendered into ASCII and then
parsed...

(disabled by passing '--noifconfig' as an argument)

* `iproute2`

the hipper, younger, alternative to ifconfig, known to its friends as 'ip'.
Nobody seems to know how it works but, unlike ifconfig, it can deal with 
one interface having more than one IP address (without having to do aliases
like eth0:0).

(disabled by passing '--noiproute2' as an argument)

* `iptables`' NATting

Addresses that are just added to the NAT chain in iptables don't appear 
anywhere else, but traffic addressed to them will be accepted, and they
will be properly routed. We retrieve the source address of every SNAT rule
and the destination address of every DNAT one.

(disabled by passing '--nonat' as an argument)



If the iproute2 or the ifconfig commands fail, this'll just silently carry on.
