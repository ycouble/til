---
title: IP Multicasting
description: Broadcasting content over IP Networks
date: March 31, 2015
categories: [networking]
image: images/blog/multicast.jpg
---




 This article tries to synthetise a few RFCs about Multicasting over IPv4.
 

Services such as TV and radio were initially designed to be broadcasted over hertzian media for which broadcasting is the standard way of communicating. 
But these services among many others had to transition to the IP world to follow and widen their audience.
  

Until here, no problem. Except with IP, broadcast services are no longer alone and they have to live along many many others.
Most of which are unicast services.

The IP protocol, as specified in [RFC 791](https://tools.ietf.org/html/rfc791), allowed one to one (unicast) and one to everyone (broadcast) only.
Simply broadcasting the traffic is not an option as it would flood every network worldwide (or more likely be blocked at the first router encountered).
Which leaves us with sending traffic to each client requesting the service in unicast mode.
  

This becomes expensive bandwidth and energy wise for the broadcast source and for the network in general after only a few clients as the content is duplicated on many segments of the network.
Reducing data duplication and optimizing network ressources while also extending the reach of the broadcasting service are the main objectives of IP Multicasting.

To better understand what is at stake with IP multicast, we can look at the figures below. 
In the case of legacy broadcasting over a dedicated network, a broadcasting point has to be deployed close to each client while with IP multicast, only the very necessary flows are generated over an already deployed network.

[![Case of broadcasted content over Hertzian media](./IP Multicasting - Notebook_files/broadcast-case.png)](http://couble.ovh/figures/broadcast-case.png)Figure 1: Case of broadcasted content over Hertzian media [[PNG](http://couble.ovh/figures/broadcast-case.png)]
[![Case of multicasted content over IP networks](./IP Multicasting - Notebook_files/multicast-case.png)](http://couble.ovh/figures/multicast-case.png)Figure 2: Case of multicasted content over IP networks [[PNG](http://couble.ovh/figures/multicast-case.png)]
### Multicast group management in IPv4

In 1989, the IETF published the informational [RFC 1112](https://www.ietf.org/rfc/rfc1112.txt) to gather recommendations on how to implement IP Multicast inside the IP stack implementations.
It also defines in the appendices the protocol IGMP. IGMP has been updated twice in 1997 (IGMPv2) and 2006 (IGMPv3).

#### Integration in the IP stack

IGMP is a part of IP, in the same way ICMP is.
IGMP only introduces a few additional services offered to the transport layer and requires an adapted Data Link layer for Multicast.
Initially, all an IP host/router has to do to be multicast compliant, is to accept and process packets destined to multicast groups if it belongs to the group, know which groups it belongs to and of course the ability to join/leave a group.

[![IGMP integration in IP stack, extracted from RFC 1112](./IP Multicasting - Notebook_files/IGMP_stack.png)](http://couble.ovh/figures/IGMP_stack.png)Figure 3: IGMP integration in IP stack, extracted from RFC 1112 [[PNG](http://couble.ovh/figures/IGMP_stack.png)]
##### Multicast IP addresses

The IANA has reserved the `224.0.0.0/4` for multicast group addresses. See [RFC 5771](http://tools.ietf.org/html/rfc5771) for more details and best practices about IPv4 multicast addresses.
  

Many of the \(2^{28}\) addresses are reserved, 
As an example, the `224.0.0.0/23` is reserved for network protocols (local & internetworks), here are a few noticeable addresses:

* `224.0.0.1`: All hosts multicast group address.
* `224.0.0.2`: All routers multicast group address.
* `224.0.0.13`: multicast group address used by PIMv2
* `224.0.0.22`: multicast group address used by IGMPv3.

A few other addresses are reserved for router redundancy/routing protocols, as the `224.0.0.18` group address which is reserved for VRRP, and `224.0.0.5-6` reserved for OSPF.
  

More generally, the IANA has divided the multicast IPv4 block into blocks, each one being destined to a specific usage.
Among these groups, we can find:

* The `233.0.0.0/8` block is the GLOP Block, with \(2^{16}\) /24 blocks assigned to each 16bits ASN.
* The `239.0.0.0/8` block for private use (equivalent in multicast of the `10.0.0.0/8` block for unicast).

##### L2 muliticast addresses

IP multicasting requires an adaptation of layers underneath IP. Here we'll only deal with Ethernet.
  

Ethernet has defined an OUI specific for Ethernet multicast addresses: `01-00-5E` (hex).
IP multicast addresses are then translated into the corresponding MAC addresses with a simple principle, copying the 23 lowest-order bits from the IP address into the 23 lowest-order bits of the MAC address.

[![IP multicast address translation into MAC multicast address.](./IP Multicasting - Notebook_files/ipm-mac-translation.png)](http://couble.ovh/figures/ipm-mac-translation.png)Figure 4: IP multicast address translation into MAC multicast address. [[PNG](http://couble.ovh/figures/ipm-mac-translation.png)]
(Credits: [The TCP IP guide](http://www.tcpipguide.com/free/t_TCPIPAddressResolutionForIPMulticastAddresses.htm))
#### Evolution of IGMP

IGMP is the protocol that enables hosts to inform routers what group they belong to, and routers to ask hosts on the LAN for this information.
The protocol reach is local, and an IGMP packets are never routed.
Besides, IGMP messages are carried within IP packets.

IGMP defines two types of actors:

* *Hosts*: the end users in a LAN who join and leave multicast groups.
* *Routers*: the IP routers who coordinate the multicast groups in a LAN. In IGMPv2 & IGMPv3, routers are diffentiated according to their IGMP role.
	+ The *Querier* is the active IGMP routers on the LAN. He is the one to ask for membership reports.
	+ The *Non-Queriers* are other routers on the LAN, implementing IGMP but not performing any IGMP action. 
	 They must be IGMP silent.

##### IGMPv1

IGMPv1 only describes how host advertise the groups they belong to, and how routers poll request host to report their memberships.
  

In IGMPv1, the IGMP router sends a *Query* to the **All Host** multicast group address (`224.0.0.1`) to know what are the multicast groups needed on the LAN.
Hosts, when recieving these queries, respond by sending a group report for each group they belong to.
  

The relevant information for the router is which groups are required on the LAN, but not how many clients there is.
Hence, in order to reduce the amount of IGMP packets flowing on the LAN right after a *Query*, hosts must set up a timer for each multicast group and send each report when its timer expires, if no other reports has been recieved for the group.
  

The IGMPv1 frame is quite simple and forecast future IGMP versions with additional data fields and packet types.

[![IGMPv1 Frame format, extracted from RFC1112](./IP Multicasting - Notebook_files/igmpv1_frame.png)](http://couble.ovh/figures/igmpv1_frame.png)Figure 5: IGMPv1 Frame format, extracted from RFC1112 [[PNG](http://couble.ovh/figures/igmpv1_frame.png)]
##### IGMPv2

Version 2 of the protocol improves IGMPv1 by enabling hosts to tell when they leave a group, allowing faster multicast stream closure and a more efficient use of bandwidth globally.
  

The IGMP frame is modified in version 2, with a max-response-time field to allow router to constrol and speed up the process of retrieving a report from a host.
Routers keep sending periodical *General Queries* to have a group membership overview for a LAN.
  

This version of the group management protocol requires hosts to send an IGMP *Leave Group* message whenever the *leave\_group* service is asked to the IP/IGMPv2 stack.
This message is sent to the **All Router** multicast group address with a new packet type.
  

When recieving a group leave report from a host, routers must perform a *Group Specific Query* to the `x.x.x.x` to check if there is at least one host belonging to the `x.x.x.x` multicast group.

[![IGMPv2 Frame format, extracted from RFC2236](./IP Multicasting - Notebook_files/igmpv2_frame.png)](http://couble.ovh/figures/igmpv2_frame.png)Figure 6: IGMPv2 Frame format, extracted from RFC2236 [[PNG](http://couble.ovh/figures/igmpv2_frame.png)]
##### IGMP Snooping Switches

IGMP describles the control plane of multicast communications.
On the data plane side, over a LAN, switches simply broadcast multicast frames by default.
Over large LANs, it becomes very inefficient in terms of bandwidth usage.

[![Impact of snooping switches on a LAN](./IP Multicasting - Notebook_files/multicast-snoopingimpact.png)](http://couble.ovh/figures/multicast-snoopingimpact.png)Figure 7: Impact of snooping switches on a LAN [[PNG](http://couble.ovh/figures/multicast-snoopingimpact.png)]

Manufacturers such as Cisco quickly developped software to allow user to configure switches to only forward multicast frames to the ports where multicast reports were recieved for that specific group.
Of course the the **All Host** multicast group ethernet address (`01-00-5E-00-00-01`) should always be broadcasted.
  

Implementations are highly manufacturer dependant, even after the IETF produced the *Best Practice* [RFC4541](https://tools.ietf.org/html/rfc4541) in 2006.
  

This mechanism reduces considerably the bandwidth used in a LAN by multicast traffic, and extends the multicast principles to the LAN.

[![How snooping switches handle multicast](./IP Multicasting - Notebook_files/multicast-snoopingdetail.png)](http://couble.ovh/figures/multicast-snoopingdetail.png)Figure 8: How snooping switches handle multicast [[PNG](http://couble.ovh/figures/multicast-snoopingdetail.png)]

Snooping switches should always broadcast a multicast frame intended for a multicast group where no report has been sent. **But**, it is not always implemented.
A common issue with IGMP snooping is that routing protocols using multicast address to communicate between routers are blocked by the snooping switch.
  

Also, IGMP snooping doesn't live well with some IGMPv1 and IGMPv2, where hosts may assume that the traffic is broadcasted and not see any interest in sending a report if another host has sent one earlier.
As a matter of fact, RFC1112 recommends that a host cancels its report-send-timer uppon reception of a report for the same multicast group.
The snooping switch, on the other end will never see a multicast frame originating from the host for that group and will not forward multicast frames towards the unfortunate host.
  

Along the IGMP snooping best practices, the IETF published a Standard Track, RFC3376 for the IGMPv3 specification, snooping aware.

##### IGMPv3

IGMPv3 was specified with a few flaws of the previous versions or improvements in mind:

* Snooping switches as discussed above.
* Hosts would often need the traffic from one source rather than the whole multicast group traffic.
   

 For example, an IPTV service could use one multicast group to broadcast all its channels, but a client of this service only needs one source's stream at a time.
* Host suppression (cancelling sending a membership report because another host already has), doesn't allow a clear host identification by routers

###### Frame format

IGMPv3 use a larger frame for its messages, and uses two different formats for *Membership Queries* and *Membership Reports*.
A few other additional fields have been added in IGMPv3 to better control multicast group members and optimize multicast flows.
  

One of the main changes is that this version allows host to aggregate their group reports inside one.
But then the report has to be sent to a generic multicast address, `224.0.0.22` dedicated to IGMPv3.
All IGMPv3 router must belong to this group as *Hosts*.
  

*Membership Query* messages are now always group specific and include a list of available multicast sources for that group.

[![IGMPv3 frame format, as specified in RFC3376](./IP Multicasting - Notebook_files/igmpv3_frame.png)](http://couble.ovh/figures/igmpv3_frame.png)Figure 9: IGMPv3 frame format, as specified in RFC3376 [[PNG](http://couble.ovh/figures/igmpv3_frame.png)]
###### Source specific multicast

As multicast sources are provided to every IGMPv3 host, they are able to choose and tell routers which sources they want to listen to.
  

This allows a more precise traffic engineering over WANs (only sources which have clients will be multicasted, and only to those interested).
I think this change was introduced because there is only so many multicast addresses for each operator compared to the amount of services that can benefit from multicast.
Source specific multicast allows a company/operator to "broadcast" (used here to say "make a content available to everyone") multiple services over a unique IP.
  

Suscribers usage can then be traced more precisely inside the multicast group, and the audience can easilly be measured for each service individually.

###### Snooping awareness

With source specific multicast, two hosts requesting membership of the same multicast group may not ask for the same sources.
Hence a lot of mecanisms such as host suppression are not relevant or necessary anymore.
Moreover removing host suppression solves all snooping problems encountered in v1 or v2.

###### Source inclusion/exclusion

There are two types of reports: 

* Inclusive reports (with a *Group Record* field set to 1 aka MODE\_IS\_INCLUDE), only ask for a limited number of sources.
* Exlusive reports (with a *Group Record* field set to 2 aka MODE\_IS\_EXCLUDE), ask for all sources except a limited number of sources

This provides more flexibility to hosts, while also factorizing the join and leave actions into one single message.
  

For example the join group report from IGMPv1/v2 is a MODE\_IS\_EXCLUDE report with no source specified, and the leave group report is a MODE\_IS\_INCLUDE report with no source specified.

#### Interoperability of different versions of IGMP

Each version of IGMP is specified to be retro-compatible, but are known to get along with each other very badly.
Generally, if the later version (v2/v3) detects an IGMP router with an earlier (v1/v2) version it automatically switches to the oldest version of the two.
  

Nevertheless, IGMPv3 can theoretically function with IGMPv1/v2 hosts in the LAN.

### Multicasting inside a LAN

IP Multicast is mainly designed for the transport and routing of multicast over networks.
But even in a LAN, it can find some useful applications.
  

Multicasting over solely a LAN provides some challenges aswell and I will try to overview some theoretical possibilities in a later article dedicated to the topic.
  

**However in the meantime, I remain open to any feedback or advises on architectures, specifications or products you may know. So don't hesitate to leave me a comment below!**

### Multicast routing

Routing multicast traffic basically consist in building the shortest tree to cover all suscribers for a service.
  

Multicast routing is another vast subject that I am not able to study and syntethize immediately, so it will hopefully be addressed in a future article.

Sources:

1. [RFC 1112: Host Extension for IP Multicasting](https://www.ietf.org/rfc/rfc1112.txt)
2. [RFC 2236: Internet Group Management Protocol, Version 2](https://www.ietf.org/rfc/rfc2236.txt)
3. [RFC 3376: Internet Group Management Protocol, Version 3](https://tools.ietf.org/html/rfc3376)
4. [RFC 5771: IANA Guidelines for IPv4 Multicast Address Assignments](http://tools.ietf.org/html/rfc5771)
5. [RFC 4541: Considerations for Internet Group Management Protocol (IGMP) and Multicast Listener Discovery (MLD) Snooping Switches](https://www.ietf.org/rfc/rfc4541.txt)
6. [Wikipedia article on PIM routing](http://fr.wikipedia.org/wiki/Protocol_Independent_Multicast)

